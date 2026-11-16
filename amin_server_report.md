**Server Nüfuzetmə Testi Haqqında Hesabat**

Report edir: Mirəliyev Amin

Report edilir:Zaur Qasımov-a

13.10.2025

**Mündəricat**

**Server 1**

> **1.1. Nmap ilə Port Scanı**
>
> **1.2.** **22,80 portu açığdı**
>
> **1.3. Gobuster ile scan etmek**
>
> **1.4. Hidden_text qovluğu oxumaq**
>
> **1.5. /Pwned.vuln-qovlugundan ftp username password tapılması**
>
> **1.6. Ftp vasitesile daxil olmaq**
>
> **1.8. Selena user kecid etmek**
>
> **1.9. Sistemde root olmaq**

**Server 2**

> **2.1. Nmap ilə Port Skandı**
>
> **2.2. 22 ve 80 portu açığdı**
>
> **2.3. Chatme saytında register olmağ**
>
> **2.4. Chatme de sqlinjection**
>
> **2.5. Scan etmek**
>
> **2.6. Jerry user qoşulmağ**
>
> **2.7. Nonaya keçid etmek**

**2.8. Sistemdə root olmaq**

**Server 1 :**

Nmap vasitəsilə **10.0.2.0** IP ünvanını scan etdim və **22**, **80** portlarının açıq olduğunu müşahidə etdim.

.<img src="server_report_assets/media/image1.png" style="width:6.775in;height:3.52083in" />

**80** portu açıq olduğuna görə IP-ni brauzerdə axtarıram və qarşıma belə bir sayt çıxır.

<img src="server_report_assets/media/image2.png" style="width:6.775in;height:3.52083in" />

Saytın inspectinə baxdıqda heç nə görmədim və bu mərhələdə Gobuster alətindən istifadə edərək **192.168.0.126** IP ünvanlı hədəf sistemdə directory scan apardım. Məqsəd serverdə gizli və ya əlçatmaz qovluqları tapmaq idi. Bunun üçün **/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt** siyahısından istifadə Scan nəticəsində “**/nothing**” və “**/hidden_text**” adlı directories aşkarlandı (Status 301). Bu nəticə sistemdə əlavə araşdırılmalı mümkün resursların olduğunu göstərir.

<img src="server_report_assets/media/image3.png" style="width:6.86667in;height:3.52083in" />

“**/Nothing**” adlı qovluqda bizə lazım olan məlumat yox idi. Daha sonra “**/hidden_text**” qovluğuna baxdıqda qarşımıza directory-lər çıxır və hamısını yoxladıqdan sonra “**/pwned.vuln**” qovluğunda FTP username və password tapıram. Daha sonra **ftp 10.0.2.10** yazaraq həmin username və password-dan istifadə edərək daxil oluram.

<img src="server_report_assets/media/image4.png" style="width:6.86667in;height:3.52083in" />

<img src="server_report_assets/media/image5.png" style="width:6.76667in;height:3.52083in" />

Burada axtarış etdikdən sonra **note.txt** tekst faylını və SSH ilə qoşulmaq üçün **id_rsa** faylını tapıram**. note.txt**-nin içini oxuyaraq istifadəçi adının **ariana** olduğunu öyrənirəm və SSH-a qoşuluram.<img src="server_report_assets/media/image6.png" style="width:6.83333in;height:3.52083in" />

İndi isə geriyə privilege escalation etmək qalır. **sudo -l** commandı ilə necə privesc edə bilərəm deyə baxıram və qarşıma **(ALL:ALL)** **SELENA /home/messenger.sh** çıxır və “**sudo -u selena /home/messenger.sh”** commandı ilə messengere girirəm.

<img src="server_report_assets/media/image7.png" style="width:6.84167in;height:3.52083in" />

**/bin/bash** istifadə edərək selena userinə yüksəlirəm və id verdikdə qarşıma docker çıxır, və bu da o deməkdir ki, docker ilə **root**-a privesc edə bilərəm.<img src="server_report_assets/media/image8.png" style="width:6.51667in;height:3.52083in" />

Axtarış nəticəsində “**docker run -it --rm alpine sh**” ilə root ola biləcəyimi görürəm və **root** oldum.

**Server 2 :**

Nmap ilə **10.0.2.9** ünvanını skan etdim və 22 (**SSH**) və 80 (**HTTP**) portlarının açıq olduğunu gördüm.

<img src="server_report_assets/media/image9.png" style="width:6.475in;height:3.52431in" />

Brauzerdən http://10.0.2.9/register.php səhifəsinə daxil oldum və qeydiyyat formasını sınadım.

Formu doldurub göndərdikdə “Account successfully created” mesajı çıxdı, yəni sistem yeni istifadəçi yaradılmasına icazə verir.

<img src="server_report_assets/media/image10.png" style="width:6.40833in;height:3.52431in" />

Daha sonra burada jerry, nona kimi istifadəçiləri gördüm və logout edərək SQL injection olub‑olmadığını yoxladım; SQL injection tapdım — belə ki **“ ’OR’01=1-- -** “ payload-ını istifadə edərək istədiyim istifadəçi kimi sistemə daxil ola bilirəm.

<img src="server_report_assets/media/image11.png" style="width:6.45in;height:3.52431in" />

Burada isə inspect ilə incələdikdən sonra jerry üçün **thatsmynonapass** passwordunu tapdım və nona üçün isə **myfriendtom** passwordunu tapdım.<img src="server_report_assets/media/image12.png" style="width:6.61667in;height:3.52431in" />

<img src="server_report_assets/media/image13.png" style="width:6.61667in;height:3.52431in" />

Daha sonra SSH ilə qoşulmağa çalışarkən username və passwordları uyğun olaraq yoxladım, amma daxil olmaq mümkün olmadı və kodların SSH üçün dəyişdirildiyini düşündüm; belə yoxlayaraq jerry istifadəçisi oldum.

<img src="server_report_assets/media/image14.png" style="width:6.70833in;height:3.52431in" />

Privesc etmək üçün axtarış etdim, amma jerry üçün sudo -l icazələri olmadığını gördüm. Sonra su nona komandası ilə nona istifadəçisinə keçdim və burada sudo -l ilə “**/usr/bin/lynx**”üçün **NOPASSWD** girişinin olduğunu gördüm. Axtarış nəticəsində “**sudo lynx**”ilə root ola biləcəyimi gördüm və komandanı işlətdikdə qarşıma pəncərə açıldı və “**!**”istifadə edərək **root** oldum.

<img src="server_report_assets/media/image15.png" style="width:6.70833in;height:3.52431in" />
