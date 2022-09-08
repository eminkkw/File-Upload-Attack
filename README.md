# File-Upload-Attack
Uygulamalar, iş akışlarına göre dosya yükleme özelliği sunabilir ve ihtiyaca göre de farklı türdeki dosya formatlarına izin verilebilir. Örneğin, bir kullanıcının profil 
fotoğrafı gibi resim dosyaları, video dosyaları veya çeşitli ofis dosyaları yükleyebilmesi gibi.
<br>
Dosya yükleme fonksiyonu, geniş bir atak yüzeyine sahiptir. Yüklenen dosyanın, adı, içeriği ve boyutu üzerinden uygulamanın akışına bağlı olarak farklı farklı güvenlik
açıkları oluşabilir. Örneğin, RCE, Injection, SSRF, XSS ve DoS… Bu durum, tamamen uygulamanın yüklenen dosyayı nasıl işlediğine göre farklılık gösterecektir.<br>
<br>
Yüklenen dosyalar ile hem sunucu taraflı hem de kullanıcı tarafına yönelik saldırılar gerçekleştirilebilir. Örneğin, uygulamaya yüklenebilecek bir webshell ile sunucunun 
doğrudan kontrolü ele geçirilebilir veya yüklenen zararlı bir dosya ile kullanıcıların tarayıcılarda istenilen javascript kodu çalıştırılabilir.<br>
<br>
Dosya yükleme fonksiyonu üzerinde oluşabilecek bazı güvenlik zafiyetleri ve bazı güvensiz kontrolleri atlatma teknikleri aşağıdaki gibidir;<br>
<br>
# Client-side-Kontrol
Kullanıcı tarafında yapılan kontrollerin kolaylıkla atlatılabileceği unutulmamalıdır. Bu kontrollerin amacı, güvenlik odaklı değil kullanıcıları yönlendirmek ve deneyimi 
arttırmaya yöneliktir. Güvenlik söz konusu olduğunda, kontrollerin sunucu tarafında yapılması gerekmektedir.<br>
<br>
Saldırgan bu adımda, zararlı dosyasının uzantısını izin verilen bir uzantı (örneğin, .jpg) ile değiştirir ardından istek tarayıcıdan çıktıktan sonra araya proxy ile girerek uzantıyı “.php” olarak değiştirdiğinde client-side kontrolü atlatarak dosyayı yükleyecektir.<br>
<br>
# Blacklist-Yaklaşımı
Yapılan bir diğer güvensiz kontrol, uzantıların kara liste yöntemi ile kontrol edilmesidir. İstenilmeyen dosya uzantısının engellenmesi sırasında geliştirici tarafından 
engellenen dosya uzantıları arasında zararlı dosyanın başka bir varyantı unutulmuş olabilir.<br>
<br>
Örneğin, geliştirici “.php” uzantısını kara listeye almış olabilir ama “.php7”, “.phtml” gibi uzantılar da PHP tarafından yorumlanır.<br>
<br>
Bu yaklaşımda saldırgan, kontrolü case-sensitive olarak veya engellenmesi unutulmuş başka bir uzantı ile geçebilir.<br>
Ek olarak, saldırgan .htaccess veya web.config gibi konfigürasyon dosyaları yükleyebilirse de komut çalıştırma imkanı bulacaktır.<br>
<br>
Uzantı kontrolleri, çift uzantı veya null byte karakterlerinin eklenmesiyle de atlatılabilir.<br>
<br>
# Content-Type-Kontrolü
Uygulama tarafında yüklenen dosyanın formatı “Content-Type” başlığına bakılarak kontrol ediliyorsa bu kontrol kolaylıkla atlatılabilir.<br>
<br>
Örnek olarak saldırgan hazırladığı zararlı PHP dosyasını yüklerken, proxy yardımı ile HTTP isteğindeki Content-Type bilgisini “application/x-httpd-php” den 
“image/jpeg” olarak değiştirdiğinde bu korumayı atlatacaktır.<br>
<br>
# Magic-Byte-Kontrolü
Sunucu tarafında yüklenen dosyanın içeriği sadece magic byte’lara bakılarak kontrol ediliyorsa, saldırgan bu adımda zararlı dosyasının magic byte’larına izin verilen formata ait magic byte’ları ekleyerek bu kontrolü geçecektir.<br>
<br>
# Dosya-Adı-Üzerinden-Oluşabilcek-Zafiyetler
Uygulama tarafında yüklenen dosyanın adı kontrol edilmediği durumlarda birçok farklı zafiyet oluşabilir.<br>
<br>
Dosya adının sunucu tarafında nerede işletildiğine bağlı olarak Injection zafiyetleri oluşabilir. Yüklenen dosya adının ekranda gösterilmesi sonucunda Cross-site 
Scripting (XSS) zafiyeti oluşabilir.<br>
<br>
file$(whoami).png # Command Injection <br>
file';select+sleep(10);--.png # SQLi<br>
file"><script>alert(1)</script>.png # Cross-site Scripting (XSS)<br>
<br>
Ek olarak, dosya adı üzerinden beklenilen dizinin dışına yazabilme ve kritik sistem dosyaları üzerine yazabilme gibi zafiyetlerde oluşabilir.<br>
<br>
Örneğin, uygulama üzerinde yüklenen dosyalar “/files” dizininin altına yazıldığını düşünelim. Saldırgan burada dizin geçişi karakterlerini kullanarak başka bir dizine 
veya var olan bir sistem dosyası üzerine yazabilir.<br>
# Zip-Slip-Zafiyeti
Uygulamaya yüklenen arşiv dosyalarının (zip, rar ve 7z gibi) çıkartılması sırasında arşiv içerisinde yer alan dosya adlarının doğru bir şekilde kontrol edilmediği 
durumlarda Zip Slip zafiyeti oluşmaktadır.<br>
<br>
Dizin geçişi karakterlerinin (../) arşiv içerisinde yer alan dosyaların adları olarak kullanılması ile zafiyet tetiklenmektedir.<br>
# Denial-of-Service (DoS)
Yüklenen dosyaların boyutları kontrol edilmediği durumda saldırgan büyük boyutlu dosyalar yükleyerek sunucunun servis veremez hale gelmesini sağlayabilir.<br>
<br>
Ek olarak, yüklenen büyük pixel’e sahip resim dosyaları, sunucu tarafında işlendiği sırada servisin yavaşlamasına ve hizmet dışı kalmasına sebep olabilir. 
(Pixel Flood)<br>
