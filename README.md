# SeCloud: Hardened Alpine Private Cloud 🌩️

<div align="center">
  <img src="https://img.shields.io/badge/OS-Alpine_Linux-0D597F?style=for-the-badge&logo=alpinelinux&logoColor=white" alt="Alpine Linux"/>
  <img src="https://img.shields.io/badge/Security-Hardened-red?style=for-the-badge&logo=security" alt="Hardened"/>
  <img src="https://img.shields.io/badge/Auth-ED25519_SSH-blue?style=for-the-badge&logo=ssh" alt="SSH"/>
</div>

---

## 🇬🇧 English

### 📝 Overview
**SeCloud** is a minimalist, highly secure, zero-trust private cloud environment built from scratch on Alpine Linux. The primary objective of this project is to provision a lightweight server footprint while enforcing strict security protocols, including disabling password-based authentication, configuring localized privilege escalation, and establishing a default-drop firewall policy.

### 🛠️ System Architecture & Tech Stack
* **Base OS:** Alpine Linux (Optimized for minimal resource consumption).
* **Access Management:** OpenSSH with strictly ED25519 Key-Based Authentication.
* **Privilege Escalation:** `doas` configured for the `wheel` group (replacing `sudo` for a smaller attack surface).
* **Network Security:** `iptables` enforcing a Default DROP policy for unhandled incoming traffic.
* **Monitoring:** `fastfetch` and `htop` for real-time resource tracking.

### 📓 Build Log & Troubleshooting
The deployment required overcoming several environment-specific challenges:

1. **Boot & Memory Allocation:**
   Resolved initial kernel panic and `Invalid physical address chosen!` errors by optimizing VM memory configurations and boot parameters.
   <br>![Memory Error](ss/memory-error.png)

2. **Network Interface & Package Mirroring:**
   Initial attempts to fetch packages resulted in `bad address` and `HTTP 403 Forbidden` errors due to DNS/routing issues. Resolved by reconfiguring the NIC to `Intel PRO/1000 MT Desktop` under NAT, allowing the `udhcpc` daemon to obtain a valid DHCP lease (`10.0.2.15`).
   <br>![Connection Errors](ss/connection-errors.png)
   <br>![Connection Stable](ss/connection-stable.png)

### 🛡️ System Hardening Implementation
Security was implemented at the core level during and after installation:

1. **Root Access Restriction:** Root password login was explicitly prohibited during the `setup-alpine` phase to prevent basic vector attacks.
   <br>![Init](ss/init.png)
   <br>![Blocking Root](ss/blocking-root.png)

2. **Secure Privilege Escalation:**
   Installed and configured `doas` to allow `permit persist :wheel`, ensuring secure and logged administrative access without relying on standard `sudo`.
   <br>![Privilege Escalation](ss/privilege%20escalation%20setup%20%28Doas%29.png)

3. **SSH Hardening & Key Verification:**
   Configured `sshd_config` to strictly enforce `PasswordAuthentication no`. Verified initial connection via ED25519 key fingerprinting.
   <br>![SSH Hardening](ss/SSH%20Hardening.png)
   <br>![Fingerprint](ss/fingerprint.png)

### 📸 Proof of Concept (PoC)
The system is fully operational, hardened, and capable of secure file operations.

**System State & Firewall:**
A lightweight footprint (~76 packages) protected by an aggressive `iptables` DROP policy, allowing only SSH (Port 22).
<br>![Fastfetch](ss/fastfetch.png)
<br>![Firewall](ss/firewall-iptables.png)

**Secure File Transfer (SCP) Validation:**
Successful transfer and read execution of `windows-to-linux.txt` from the host machine to the hardened guest via Secure Copy Protocol.
<br>![First Interaction](ss/first%20interaction.png)
<br>![Final File Read](ss/windows-to-linux%28final.png)

---

## 🇹🇷 Türkçe

### 📝 Genel Bakış
**SeCloud**, Alpine Linux üzerinde sıfırdan inşa edilmiş, "sıfır güven" (zero-trust) prensibine dayalı minimalist ve yüksek güvenlikli bir özel bulut (private cloud) ortamıdır. Bu projenin temel amacı, parola tabanlı kimlik doğrulamayı devre dışı bırakmak, güvenli yetki yükseltme politikaları belirlemek ve katı güvenlik duvarı (firewall) kuralları uygulayarak siber saldırılara karşı zırhlı bir altyapı oluşturmaktır.

### 🛠️ Sistem Mimarisi ve Teknolojiler
* **İşletim Sistemi:** Alpine Linux (Minimum kaynak tüketimi için optimize edilmiştir).
* **Erişim Yönetimi:** Yalnızca ED25519 Anahtar Tabanlı Kimlik Doğrulamaya (Key-Based Auth) izin veren OpenSSH.
* **Yetki Yükseltme:** Saldırı yüzeyini daraltmak amacıyla `sudo` yerine `wheel` grubu için yapılandırılmış `doas` aracı.
* **Ağ Güvenliği:** İzinsiz tüm gelen trafiği reddeden "Default DROP" politikasına sahip `iptables`.

### 📓 Kurulum Günlüğü ve Sorun Giderme (Troubleshooting)
Sistemin inşası sırasında karşılaşılan çevresel engeller ve çözüm yöntemleri:

1. **Boot ve Bellek Hataları:**
   İlk önyükleme sırasında karşılaşılan kernel panic ve `Invalid physical address chosen!` hataları, sanal makine bellek ve boot parametrelerinin optimize edilmesiyle çözüldü.
   <br>![Memory Error](ss/memory-error.png)

2. **Ağ Bağlantısı ve Depo (Repository) Çözümlemesi:**
   Paket çekme işlemleri sırasında alınan `bad address` ve `403 Forbidden` ağ hataları, sanal ağ bağdaştırıcısının (NIC) `Intel PRO/1000 MT Desktop` olarak değiştirilmesiyle giderildi. `udhcpc` servisi başarıyla IP ataması gerçekleştirdi (`10.0.2.15`).
   <br>![Connection Errors](ss/connection-errors.png)
   <br>![Connection Stable](ss/connection-stable.png)

### 🛡️ Sistem Zırhlama (Hardening) Aşamaları
Güvenlik, kurulum aşamasından itibaren çekirdek düzeyde uygulanmıştır:

1. **Root Erişiminin Kısıtlanması:** `setup-alpine` konfigürasyonu sırasında, brute-force saldırılarını baştan engellemek için root parolası ile giriş (`prohibit-password`) yasaklandı.
   <br>![Init](ss/init.png)
   <br>![Blocking Root](ss/blocking-root.png)

2. **Güvenli Yönetici İzinleri (Privilege Escalation):**
   Geleneksel `sudo` yerine `doas` kurularak `permit persist :wheel` yapılandırması sağlandı. Böylece yönetici işlemleri güvenli bir çembere alındı.
   <br>![Privilege Escalation](ss/privilege%20escalation%20setup%20%28Doas%29.png)

3. **SSH Zırhlama (Hardening) ve Anahtar Doğrulaması:**
   `sshd_config` dosyası üzerinden `PasswordAuthentication no` kuralı zorunlu kılındı. Sisteme ilk giriş, ED25519 anahtar parmak izi (fingerprint) doğrulaması ile şifreli tünelden yapıldı.
   <br>![SSH Hardening](ss/SSH%20Hardening.png)
   <br>![Fingerprint](ss/fingerprint.png)

### 📸 Kavram Kanıtı (Proof of Concept - PoC)
Sistem tamamen çalışır durumda, zırhlanmış ve güvenli dosya transferine hazırdır.

**Sistem Durumu ve Güvenlik Duvarı:**
Sadece ~76 paket ile çalışan hafif yapı ve yalnızca 22. portu (SSH) dışarıya açık bırakan agresif `iptables` DROP kalkanı.
<br>![Fastfetch](ss/fastfetch.png)
<br>![Firewall](ss/firewall-iptables.png)

**Güvenli Dosya Transferi (SCP) Doğrulaması:**
Ana makineden (Windows), zırhlı sunucuya (Linux) SCP protokolü kullanılarak `windows-to-linux.txt` dosyasının başarıyla aktarılması ve okunması.
<br>![First Interaction](ss/first%20interaction.png)
<br>![Final File Read](ss/windows-to-linux%28final.png)
