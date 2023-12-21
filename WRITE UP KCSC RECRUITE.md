# WRITE UP KCSC RECRUITE
# Nguyễn Minh Triết_AT20N0155_Miền Nam

## REVERSE

**I. RealWarmup**
* Load chall vào `Cutter` để disas file `chall.exe` 
![Screenshot from 2023-12-16 22-43-47](https://hackmd.io/_uploads/SJwyJg3L6.png)

* nhận thấy đoạn code chương trình yêu cầu nhập vào một chuỗi và so sánh với chuỗi mặc định(đã được Encode B64)
* Decode B64 chuỗi `S0NTQ3tDaDQwYF9NfF98bjlgX0QzTidfVjAxJ183N1wvX0tDU0N9` sẽ được Flag 
> `KCSC{Ch40`_M|_|n9`_D3N'_V01'_77\/_KCSC}`

**II. Hide and seek**
* Giải nén chall sẽ nhận được 1 file `dropFile.exe` chạy thử file thì sẽ thấy file flag đã bị drop ở đâu đó trong ổ `C` kèm theo đường link bị ẩn.

* Copy paste đường link bị ẩn sang notepad ta được `C:\Users\NGUYEN MINH TRIET\AppData\Local\Temp\.temp_html_file_76459343.html` lần theo đường link này sẽ lấy đường link flag `file:///C:/Users/NGUYEN%20MINH%20TRIET/AppData/Local/Temp/%E2%80%AEtemp_html_file_77533890.html` ấn vào `View secret`...=)) À thôi ấn vào view Flag và submit thôi 
> `KCSC{~(^._.)=^._.^=(._.^)~}`

**III. Images**

* File này chỉ cho mỗi hình ảnh nên k thể debug để xem flag được nên em phải ngồi đọc từng dòng=((. Ngồi mò cả tiếng thì cũng hiểu được kha khá


![4](https://hackmd.io/_uploads/SyxqKu3Lp.jpg)
* Nhìn điểm chung ở các commnad ở các ảnh giữa thì đề lấy lệnh `cmp` thanh `eax` với 1 số, nên em nghĩ đấy sẽ là các kí tự của flag, nhìn vào lệnh `imul` thì sẽ là thứ tự của các kí tự được `cmp` thông qua mỗi cmd `[rbp+rax+230h+Buffer]` để lưu `value` của `eax` vào.

* Để nhanh gọn thì em viết code python để sắp lại thứ tự các kí tự
```
lst = [75, 110, 101, 104, 101, 110, 107, 110, 95, 111, 67, 95, 110, 95, 118, 97, 67, 105, 121, 95, 104, 110, 109, 110, 104, 100, 111, 97, 110, 100, 104, 95, 95, 110, 97, 95, 116, 95, 105, 103, 110, 97, 95, 96, 125, 123, 105, 95, 97, 83, 67]

lst1 = [0, 0x1a, 0x14, 0x18, 34, 23, 18, 21, 22, 9, 5, 8, 10, 14, 12, 16, 3, 30, 48, 41, 44, 39, 7, 28, 29, 15, 38, 42, 46, 37, 33, 32, 36, 31, 25, 27, 35, 45, 19, 40,43,47,17,49,50, 4,13,11,6,2,1]
lst2 =([chr(x) for _,x in sorted(zip(lst1,lst))])
print(''.join(lst2))
```
> `KCSC{Cam_on_vi_da_kien_nhan_nhin_het_dong_anh_nay`}`


---


## CRYPTO

**I.EZ_Ceasar**

* Mở file source lên 
```
import string
import random

alphabet = string.ascii_letters + string.digits + "!{_}?"

flag = 'KCSC{s0m3_r3ad4ble_5tr1ng_like_7his}'
assert all(i in alphabet for i in flag)

key = random.randint(0, 2**256)

ct = ""
for i in flag:
    ct += (alphabet[(alphabet.index(i) + key) % len(alphabet)])

print(f"{ct=}")

# ct='ldtdMdEQ8F7NC8Nd1F88CSF1NF3TNdBB1O'
```

* Thấy rằng code trên encode flag thành ct bằng cách thay thế từng ký tự trong flag bằng một ký tự mới được tính toán dựa trên khóa key ngẫu nhiên từ 0-2**256. Vậy chỉ cần viết code ngược dựa trên key và alphabet ban đầu để decode flag 
```
import string 
import random

alphabet = string.ascii_letters + string.digits + "!{_}?"

ct = 'ldtdMdEQ8F7NC8Nd1F88CSF1NF3TNdBB1O'
key = random.randint(0, 2**256)  # The original key value is not known

flag = ""
for i in ct:
    flag += (alphabet[(alphabet.index(i) - key) % len(alphabet)])

print(f"{flag=}"
```
* Run code và chờ nhân phẩm để lấy flag=)))
> `KCSC{C3as4r_1s_Cl4ss1c4l_4nd_C00l}`

**II. Ceasar_but_Harder!!!**

* Mở source code lên ngắm nghía chall
```
import string
import random

flag = "KCSC{s0m3_r3ad4ble_5tr1ng_like_7his}" 

alphabet = string.ascii_letters + string.digits + "!{_}?"
assert all(i in alphabet for i in flag)


for i in range(3):
    k = random.randint(0, len(alphabet))
    alphabet = alphabet[:k] + alphabet[k+1:]

key = random.randint(0, 2**512)

ct = ""
for i in flag:
    ct += (alphabet[(alphabet.index(i) + key) % len(alphabet)])

print(f"{ct=}")

# ct='2V9VnRcNosvgMo4RoVfThg8osNjo0G}mmqmp'
```

* Bài này cũng encode bằng `Caesar` như bài trước nhưng 
đoạn `for i in range(3):
k = random.randint(0, len(alphabet))
alphabet = alphabet[:k] + alphabet[k+1:]` là bỏ 3 kí tự trong xâu alphabet. Mặc dù key lớn nhưng mà sau khi cho độ dài xâu alphabet thì ta chỉ cần bruteForce Tiếp đến ta chỉ cần bruteForce 3 vị trí cần xoá và descrypt dựa trên alphabet mới là xong

```
import string
import random

flag = "KCSC{s0m3_r3ad4ble_5tr1ng_like_7his}" 
ct = '2V9VnRcNosvgMo4RoVfThg8osNjo0G}mmqmp'
flag2=[]
alphabett = string.ascii_letters + string.digits + "!{_}?"
n = len(alphabett)
#print(n)
for l in range(n):
    alphabetl = alphabett[:l] + alphabett[l+1:]
    for j in range(l+1, n):
        alphabetj = alphabetl[:j] + alphabetl[j+1:]
        for k in range(j+1, n):
            alphabetk = alphabetj[:k] + alphabetj[k+1:]
            #print(len(alphabetk))
            for key in range(len(alphabetk)):
                fl=''
                for i in ct:
                    if i not in alphabetk:
                        continue
                    fl += (alphabetk[(alphabetk.index(i) - key) % len(alphabetk)])
                #print(fl)
                if 'KCSC{' in fl:
                    flag2.append(fl)
                    
for i in flag2:
    print(i)
```

* Chọn cái nào đẹp đẹp, đúng đúng nhất đem đi submit 
> KCSC{y0u_be4t_My_C3A54R_bu7_HoW!!?!}

---

## PWN

**A gift for pwners**

* Dùng lệnh `Strings gift` đọc chall sẽ lấy được đoạn đầu `KCSC{A_gift_` và đoạn cuối `pwners_0xdeadbeef}`

* Load vào `Cutter` sẽ lấy được đoạn `for_the_`

> `KCSC{A_gift_for_the_pwners_0xdeadbeef}`

---

## FOR

**VBS**

* Tải và chall về thì đã bị lock, em dùng `john` với wordlist `rockyou` để brute force unlock lấy được pass là `kma9239`

* Unlock file thì `strings code.vbs` để đọc file 

```
Dim http : Set http = CreateObject("MSXML2.ServerXMLHTTP.6.0")
Dim url : url = "http://api.bitly.com/v3/shorten?login=YOUR_BITLY_USERNAME&apiKey=YOUR_BITLY_API_KEY&longUrl=https://pastebin.pl/view/a60d3da5"

http.Open "GET", url, False
http.setRequestHeader "Content-Type", "text/xml"
http.send ""

If http.Status = 200 Then
    Dim response : response = http.responseText
    Dim shortUrl : shortUrl = ParseJson(response)("data")("url")
    WScript.Echo "Shortened URL: " & shortUrl
Else
    WScript.Echo "Error: " & http.Status & " - " & http.statusText
End If

Function ParseJson(json)
    Dim objJson : Set objJson = CreateObject("MSJSON.Parser")
    Set ParseJson = objJson.Parse(json)
End Function

```
    
* Đọc `http://api.bitly.com/v3/shorten?login=YOUR_BITLY_USERNAME&apiKey=YOUR_BITLY_API_KEY&longUrl=https://pastebin.pl/view/a60d3da5` thấy nó kêu `Username` với `API key` ở Bitly vô nên em thay thử và get flag `{"status_code":500,"status_txt":"MISSING_ARG_ACCESS_TOKEN","data":[]}` cứ tưởng là flag-_-

* nhìn kỹ lại thì ở cuối url có link `https://pastebin.pl/view/a60d3da5` truy cập vào thì em thấy flag bị encode bằng B64 nên em decode và get flag 

![Screenshot 2023-12-17 204305](https://hackmd.io/_uploads/H1CVounI6.png)

> KCSC{Vb4_h01_lor""_ae_th0ng_c4m_=(((}

## WEB

**Mỳ tôm thanh long**

* Em dùng `dirsearch -u http://103.162.14.116:10002/` để coi có folder ẩn nào trong web ko
```

Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 25 | Wordlist size: 11460

Output File: /home/trongtam/reports/http_103.162.14.116_10002/__23-12-17_09-38-51.txt

Target: http://103.162.14.116:10002/

[09:38:51] Starting: 
[09:38:59] 403 -  282B  - /.ht_wsr.txt                                      
[09:38:59] 403 -  282B  - /.htaccess.orig                                   
[09:38:59] 403 -  282B  - /.htaccess.save
[09:38:59] 403 -  282B  - /.htaccess.bak1
[09:38:59] 403 -  282B  - /.htaccess_extra                                  
[09:38:59] 403 -  282B  - /.htaccessBAK                                     
[09:38:59] 403 -  282B  - /.htaccessOLD2
[09:38:59] 403 -  282B  - /.htaccessOLD                                     
[09:38:59] 403 -  282B  - /.htaccess_orig
[09:38:59] 403 -  282B  - /.html                                            
[09:38:59] 403 -  282B  - /.htm                                             
[09:38:59] 403 -  282B  - /.htpasswd_test                                   
[09:38:59] 403 -  282B  - /.htpasswds                                       
[09:38:59] 403 -  282B  - /.htaccess_sc                                     
[09:38:59] 403 -  282B  - /.htaccess.sample                                 
[09:38:59] 403 -  282B  - /.httr-oauth                                      
[09:39:57] 200 -  346B  - /flag.txt                                         
[09:40:02] 301 -  326B  - /images  ->  http://103.162.14.116:10002/images/  
[09:40:02] 403 -  282B  - /images/                                          
[09:40:03] 403 -  282B  - /includes/                                        
[09:40:03] 301 -  328B  - /includes  ->  http://103.162.14.116:10002/includes/
[09:40:25] 301 -  325B  - /pages  ->  http://103.162.14.116:10002/pages/    
[09:40:25] 403 -  282B  - /pages/                                           
[09:40:40] 403 -  282B  - /server-status                                    
[09:40:40] 403 -  282B  - /server-status/
                                                                             
Task Completed                                                                                                                                                             
```

* Thấy file flag.txt em liền vào xem thử thì có flag 

> KCSC{Lan_Dau_Tien_Trai_Thanh_Long_Co_Trong_KCSC:))}


