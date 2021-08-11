# SPANNING-TREE-PROTOCOL
## STP (SPANNING TREE PROTOCOL) NEDİR?
STP (Spanning Tree Protokolü) IEEE 802.1 standardı olup switchlerin haberleşmesi sırasında oluşabilecek sonsuz döngüleri (loop) önlemek için keşfedilen bir protokoldür. Cisco switchlerde STP, varsayılan aktif olarak gelir ve oluşabilecek sonsuz döngüleri engellemek için bazı portları bloklar. Switchler bu loopları keşfetmek ve kendi aralarında konuşmak için birbirlerine BPDU(Bridge Protocol Data Unit) mesajları yollarlar.
### BAZI STP KAVRAMLARI
 _Root Bridge :_ STP algoritmasına göre bir tane Root Bridge seçilir ve bu Root Bridge üzerinden BPDU mesajları toplanmaya başlanır.
 
_Root Port :_ Root Bridge’e en yakın veya direkt bağlı olan(uplink) port Root Port’tur.

_Designated Port :_ Root port dışında kalan portlar için yeni bir eleme başlar. Bu elemede şuna dikkat edilir. Root bridge’e gidilen yollara bakılır ve en düşük costlu olan portlar Designated Port olarak adlandırılır.

_Blocked Port :_ Designated port seçimi yapılırken en yüksek costlu olan porta da Blocked Port ismi verilir ve bu port herhangi bir değişiklik olmazsa sonsuz döngüyü engellemek için bloklanmış durumda kalır.
_Alternate Port :_ Blocked port görevi görür, Designated port seçimi yapılırken seçim sırasında Alternate Port durumunda beklerler.

### STP ÇEŞİTLERİ
##### 1. STP (SPANNING TREE PROTOCOL)
STP, IEEE 802.1d standardı olup sonsuz döngüleri önler. STP işlemini tamamlama süresi (convergence time) yaklaşık 50 saniyedir. STP işlemleri aşağıdaki gibidir:

_Forwarding_-> RP ve DP iletişim başlar.
_Learning_-> Tüm bilgiler bridgeler tarafından öğrenilir.
_Listening_-> Root bridge, root port seçilir.
_Blocking_-> BPDU mesaj trafiği olur ama iletişim olmaz.
_Disabled_-> Her port ilk başta disable durumda olur.

##### 2. RSTP (RAPID SPANNING TREE PROTOCOL)
RSTP, IEEE 802.1W standardı olup STP gibi sonsuz döngüleri önler. Farklı olarak, bu işlemleri tamamlama süresi (convergence time) STP’ye göre daha azdır. Yani RSTP daha kısa sürede işlemleri tamamlar. STP’de bu işlem 5 adımda gerçekleşirken RSTP’de bu işlem 3 adımda gerçekleşir:

_Forwarding_-> İletişim başlar.
_Learning_-> Learning+ Listening
_Discarding_-> BPDU mesaj trafiği başlar.

##### 3. MSTP (MULTIPLE SPANNING TREE PROTOCOL)
MSTP, IEEE 802.1S standardı olup STP gibi sonsuz döngüleri önler. RSTP gibi çalışır, yani işlemleri tamamlama süresi (convergence time) azdır. Ama RSTP’de portlarda bloklama işlemi yapılırken MSTP’de yapılmaz.

VLAN grupları için instance oluşturulur ve STP hesaplamaları her instance bazında yapılır. Böylece “Load Balance (Yük Dengeleme)” sağlanır.
##### 4. PVST (PER VLAN SPANNING TREE PROTOCOL)
PVST, STP gibi sonsuz döngüleri önler. Genellikle MSTP ile karıştırılır. Fakat PVST, her VLAN için bir instance oluşturulur. MSTP de ise çoklu VLAN grupları için bir instance oluşturulur. Böylece PVST sayesinde her VLAN’a bir instance olacağından daha ölçeklenebilir bir topoloji elde edilir.

### STP NASIL ÇALIŞIR?
##### 1. ROOT BRIDGE SEÇİMİ
İlk başta herkes ben Root Bridge’im diyerek birbirlerine BPDU mesajları yollamaya başlarlar. Bu BPDU mesaj içeriğinde Bridge ID bulunmaktadır.

_Bridge ID (BID)_ = Bridge Priority + MAC Adres

Bridge ID’si düşük olan Root Bridge olmaya hak kazanır. Yani düşük priority’e sahip olan switch Root Bridge olur. Eğer priority değerleri yeniden konfigüre edilmediyse varsayılan olarak aynı üreticiye ait switchlerin priority’si genellikle aynıdır. Bu yüzden bir sonraki aşamada MAC adrese bakılır ve sayısal değer açısından en düşük MAC adresine sahip switch Root Bridge seçilir. Root bridge artık superior BPDU yollamaya başlar.
##### 2. ROOT PORT SEÇİMİ
Superior BPDU paketini alan portlar Root Port olarak etiketlenir. Root Bridge’e en yakın veya direkt bağlı olan düşük costlu portlar Root Port olarak seçilir. Seçim olurken 3 kritere dikkat edilir:
-Düşük kümülatif cost
-Düşük upstream BID
-Düşük Port ID
*Yüksek bandwidth demek düşük cost demektir. Yani bandwidth’i yüksek olan ( cost’u düşük olan) kazanır.
##### 3. DESIGNATED PORT
En iyi BPDU paketini gönderen portlar Designated Port olarak etiketlenir. Root Bridge’in bütün portları Designated Port’tur. Root bridge’in portları asla block duruma düşemez.
##### 4. BLOCKED PORT
Desginated port ve root port dışında kalan loop’a sebep olabilecek portlar blocked port durumundadır.
##### 5. ALTERNATE PORT
RSTP’de bloklanmış halde olan ancak özel durumlara sahip olan port tipleri Alternate Port olarak etiketlenir.
##### 6. BACKUP PORT
RSTP’de bloklanmış halde bulunan port tipleri Backup Port olarak etiketlenir. Diğer bir deyişle yedek olan porttur.
- Herhangi bir Designated Port’un kopması, iptal olması vb. durumda yeniden bir STP hesaplaması yapılır ve yedekte bekleyen portlar arasında seçim yapıldıktan sonra yeni Designated Port belirlenir. Designated Port durumda olan portlar iletim sağlarken bloklanmış olan portlar iletim sağlamaz.
- Topolojide herhangi bir kopma, iptal vb durumda sistem her seferinde STP hesaplamalarını yeniden yapacaktır ve her seferinde başka seçimler gerçekleşebilir. Port altında bazı komutlar sayesinde sistem sabitlenebilir. Çünkü her seferinde Root Bridge, Root Port vs. değişmesi istenmez. Bu komutlar aşağıda belirtilmiştir:

_Uplink-fast_-> Roota giden portlar hiçbir zaman blok durumda olmaz.

_Port fast_ -> Convergence time azalır. Fakat bu porta herhangi bir switch takılabilir ve STP hesaplamalarını bozabilir.

_BPDU guard_-> Port fast aktif edildikten sonra STP hesaplarının bozulmaması için mutlaka BPDU guard aktif edilmeli.

_Root guard_-> Root switch’in designated portlarına root guard denilirse bir daha hiçbir switch root olamaz. Çünkü Root switch, TCN (Topology Change Notification) yapar.

_Loop guard_-> Yazılımsal hatalar için kullanılır. 

_Udld (Unidirectional Link Detection)_->Donanımsal hatalar için kullanılır, sadece fiberde kullanılabilir.
