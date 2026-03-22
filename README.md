# Packet Tracer VLAN ve Trunk Yapılandırması

Bu proje, Cisco Packet Tracer üzerinde iki switch arasında temel VLAN (Virtual Local Area Network) ve Trunk bağlantı yapılandırmasını içermektedir.

## Topoloji Bilgileri

Ağ topolojisinde 2 adet Switch (2960) ve 4 adet PC bulunmaktadır. Cihazlar iki farklı VLAN'a ayrılarak izole edilmiştir. Farklı switch'ler üzerindeki aynı VLAN'ların haberleşebilmesi için aradaki bağlantı Trunk olarak yapılandırılmıştır.

* **VLAN 10 Cihazları:**
    * PC0 (Switch0 - Fa0/1): `192.168.1.10 /24`
    * PC2 (Switch1 - Fa0/1): `192.168.1.11 /24`
* **VLAN 20 Cihazları:**
    * PC1 (Switch0 - Fa0/2): `192.168.1.20 /24`
    * PC3 (Switch1 - Fa0/2): `192.168.1.21 /24`
* **Trunk Bağlantısı:**
    * Switch0 (Fa0/3) <---> Switch1 (Fa0/3)

## Yapılandırma Adımları

Projeyi sıfırdan oluşturmak veya incelemek isteyenler için uygulanan temel komutlar aşağıdadır. Bu komutlar her iki switch için de geçerlidir.

### 1. VLAN'ların Oluşturulması
Öncelikle switch'lerin VLAN veritabanında gerekli ağlar oluşturulmuştur:
```text
enable
configure terminal
vlan 10
vlan 20
exit
```

### 2. Access Portlarının Atanması

PC'lerin switch'lere bağlandığı portlar, ait oldukları VLAN'lara Access portu olarak atanmıştır:
```text
! VLAN 10 ataması (PC0 ve PC2 için)
interface fa0/1
switchport mode access
switchport access vlan 10
exit

! VLAN 20 ataması (PC1 ve PC3 için)
interface fa0/2
switchport mode access
switchport access vlan 20
exit
```

### 3. Trunk Bağlantısının Kurulması

Birden fazla VLAN trafiğinin (802.1q etiketleriyle) iki switch arasında taşınabilmesi için aradaki bağlantı portu Trunk moduna alınmıştır:
```text
interface fa0/3
switchport mode trunk
end
write memory
```

### Test ve Doğrulama

Yapılandırmanın başarıyla çalıştığını test etmek için şu adımlar izlenebilir:

1-Aynı VLAN İçi İletişim: PC0 (192.168.1.10) komut istemcisinden PC2'ye (192.168.1.11) ping atın. İşlem başarılı olmalıdır.

2-VLAN İzolasyonu Kontrolü: PC0'dan VLAN 20'deki bir cihaza (örneğin PC1 - 192.168.1.20) ping atın. İşlem başarısız olmalıdır (Ortamda Inter-VLAN Routing yapacak bir Router bulunmadığı için VLAN'lar arası trafik izoledir).
