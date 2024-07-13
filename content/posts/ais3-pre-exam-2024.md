+++
title = 'AIS3 Pre-Exam&MyfirstCTF 2024 Write-up'
date = 2024-07-13T18:12:18+08:00
draft = false
+++
## Misc
### Welcome
![image](https://hackmd.io/_uploads/BkOL3lG40.png)
給新手一個見面禮
```
AIS3{Welc0me_to_AIS3_PreExam_2o24!}
```
### Quantum Nim Heist
![image](https://hackmd.io/_uploads/rJTn3gMNR.png)
遊戲畫面如下:
![image](https://hackmd.io/_uploads/HyCDMWz4R.png)
> 在玩這個遊戲時，不要被規則侷限，不知道要怎麼解的話，多玩幾次就知道了
> 資安也是一樣的  
> by 某個好心的AIS3 Staff （對不起我忘記你叫甚麼了；；）

不要被規則侷限，我就想到，~~如果亂輸入數字會發生甚麼事~~

所以玩一玩就開始打一些奇怪的數字，發現居然會略過自己的回合

所以我直接打數字打到剩一個石頭就得到Flag了
```
AIS3{Ar3_y0u_a_N1m_ma57er_0r_a_Crypt0_ma57er?}
```
### Three Dimensional Secret
![image](https://hackmd.io/_uploads/rkmtRWXEA.png)
解開以後是一個封包檔案

說到封包，自然不能離開WireShark

所以把這個檔案用WireShark看，然後找資料的部分，然後用TCP Stream

就會看到一堆神奇的東西

![image](https://hackmd.io/_uploads/r1xWtkfQV0.png)

這題就跟 [picoCTF的某題](https://play.picoctf.org/practice/challenge/116?page=1&search=feed) 大概相同

所以同樣把他貼到[NC viewer(G-code檢視的網站)](https://ncviewer.com/)上
![image](https://hackmd.io/_uploads/ByxoSzm4R.png)
就有Flag了！
```
AIS3{b4d1y_tun3d_PriN73r}
```
## Web
### Evil Calculator
![image](https://hackmd.io/_uploads/r1d_P7QVA.png)

直接點網址，進去以後長這樣
![image](https://hackmd.io/_uploads/HkGt_77ER.png)
養成一個好習慣，看到Web按F12，發現了這個
```html
<input type="text" class="calculator-screen" id="expression" placeholder="0" disabled>
```
我的作法是把 disabled 改成 enable

這樣計算機的螢幕就變成了我們可以輸入文字或指令的地方

然後看source code，發現是flask框架，然後flag的路徑就在上層

所以，直接在螢幕上打open('../flag').read()

就直接噴出了Flag
```
AIS3{7RiANG13_5NAK3_I5_50_3Vi1} 
```
## Reverse
### The Long Print
![image](https://hackmd.io/_uploads/rkxV9XQE0.png)
解壓縮出來後，是一個執行檔，執行看看

結果發現，好像甚麼都沒有發生？
![image](https://hackmd.io/_uploads/r1ls4dXVC.png)

希望我們有足夠時間去收他的Flag？我才不要！

所以我們可以直接用IDA pro或者Ghidra之類的靜態分析工具，分析看看他的程式

這邊使用Ghidra分析，結果分析出來的Main函式如下

```cpp
undefined8 main(void)
{
  uint local_14;
  int local_10;
  int local_c;
  
  puts("Hope you have enough time to receive my flag:");
  for (local_10 = 0; local_10 < 0x18; local_10 = local_10 + 2) {
    local_14 = *(uint *)(&key + (ulong)*(uint *)(secret + (long)(local_10 + 1) * 4) * 4) ^
               *(uint *)(secret + (long)local_10 * 4);
    for (local_c = 0; local_c < 4; local_c = local_c + 1) {
      sleep(0x3674);
      printf("%c",(ulong)local_14);
      local_14 = local_14 >> 8;
      fflush(stdout);
    }
  }
  puts("\rOops! Where is the flag? I am sure that the flag is already printed!");
  return 0;
}
```
在第13行可以看到，他會進行0x3674秒的sleep，大概是3.872小時，太久了！

所以我們可以直接修改他的數值，直接利用Ghidra的Patch Instruction功能去修改他！

![image](https://hackmd.io/_uploads/Hy9p8OmVA.png)

把上圖的0x3674修改成0x1之類的數字，之後sleep就變成了1秒！
然後匯出之後執行檔案，沒多久就可以執行出Flag了！
![image](https://hackmd.io/_uploads/SJj6Pd7VA.png)
AIS3{You_are_the_master_of_time_management!!!!?}
### 火拳のエース
![image](https://hackmd.io/_uploads/ryTo5m74C.png)

跟上題一樣，解壓縮之後，看看題目執行的樣子
結果出現如下圖：
![image](https://hackmd.io/_uploads/ryrz9dXEC.png)
然後打開Ghidra跑分析，接下來看到main函式
```cpp
undefined4 main(void)
{
  char cVar1;
  uint __seed;
  int iVar2;
  int local_14;
  
  __seed = time((time_t *)0x0);
  srand(__seed);
  buffer0 = (char *)malloc(9);
  buffer1 = (char *)malloc(9);
  buffer2 = (char *)malloc(9);
  buffer3 = (char *)malloc(9);
  memset(buffer0,0,9);
  memset(buffer1,0,9);
  memset(buffer2,0,9);
  memset(buffer3,0,9);
  print_flag();
  __isoc99_scanf("%8s %8s %8s %8s",buffer0,buffer1,buffer2,buffer3);
  xor_strings(buffer0,&DAT_0804a163,buffer0);
  xor_strings(buffer1,&DAT_0804a16c,buffer1);
  xor_strings(buffer2,&DAT_0804a175,buffer2);
  xor_strings(buffer3,&DAT_0804a17e,buffer3);
  for (local_14 = 0; local_14 < 8; local_14 = local_14 + 1) {
    cVar1 = complex_function((int)buffer0[local_14],local_14);
    buffer0[local_14] = cVar1;
    cVar1 = complex_function((int)buffer1[local_14],local_14 + 0x20);
    buffer1[local_14] = cVar1;
    cVar1 = complex_function((int)buffer2[local_14],local_14 + 0x40);
    buffer2[local_14] = cVar1;
    cVar1 = complex_function((int)buffer3[local_14],local_14 + 0x60);
    buffer3[local_14] = cVar1;
  }
  iVar2 = strncmp(buffer0,"DHLIYJEG",8);
  if (iVar2 == 0) {
    iVar2 = strncmp(buffer1,"MZRERYND",8);
    if (iVar2 == 0) {
      iVar2 = strncmp(buffer2,"RUYODBAH",8);
      if (iVar2 == 0) {
        iVar2 = strncmp(buffer3,"BKEMPBRE",8);
        if (iVar2 == 0) {
          puts("Yes! I remember now, this is it!");
          goto LAB_08049869;
        }
      }
    }
  }
  puts("It feels slightly wrong, but almost correct...");
LAB_08049869:
  free(buffer0);
  free(buffer1);
  free(buffer2);
  free(buffer3);
  return 0;
}
```
可以看到，Flag剩下的部分是先由xor加密過後再用complex_function去再次加密

之後的四個字串就是"DHLIYJEG","MZRERYND","RUYODBAH","BKEMPBRE"

然後可以看看complex_function

```cpp
int complex_function(int param_1,int param_2)

{
  int iVar1;
  int local_10;
  
  if ((0x40 < param_1) && (param_1 < 0x5b)) {
    local_10 = (param_1 + -0x41 + param_2 * 0x11) % 0x1a;
    iVar1 = param_2 % 3 + 3;
    param_2 = param_2 % 3;
    if (param_2 == 2) {
      local_10 = ((local_10 - iVar1) + 0x1a) % 0x1a;
    }
    else if (param_2 < 3) {
      if (param_2 == 0) {
        local_10 = (local_10 * iVar1 + 7) % 0x1a;
      }
      else if (param_2 == 1) {
        local_10 = (iVar1 * 2 + local_10) % 0x1a;
      }
    }
    return local_10 + 0x41;
  }
  puts("It feels slightly wrong, but almost correct...");
                    /* WARNING: Subroutine does not return */
  exit(1);
}
```
我的作法是直接寫程式去對照

解密出來的結果再拿回去xor

程式如下！
```cpp
#include <stdio.h>
#include <string.h>
using namespace std;

void xor_strings(char *str, unsigned char *key, int len) {
    for (int i = 0; i < len; i++) {
        str[i] = str[i] ^ key[i];
    }
}

int complex_function(int param_1, int param_2) {
    int iVar1;
    int local_10;

    if ((0x40 < param_1) && (param_1 < 0x5b)) {
        local_10 = (param_1 - 0x41 + param_2 * 0x11) % 0x1a;
        iVar1 = param_2 % 3 + 3;
        param_2 = param_2 % 3;
        if (param_2 == 2) {
            local_10 = ((local_10 - iVar1) + 0x1a) % 0x1a;
        }
        else if (param_2 < 3) {
            if (param_2 == 0) {
                local_10 = (local_10 * iVar1 + 7) % 0x1a;
            }
            else if (param_2 == 1) {
                local_10 = (iVar1 * 2 + local_10) % 0x1a;
            }
        }
        return local_10 + 0x41;
    }
    printf("It feels slightly wrong, but almost correct...\n");
    return 0;
}

void decrypt(char a[], int b) {
    char ans[26];
    for (int i = 0; i < 26; i++) {
        ans[i] = 'A' + i;
    }
    for (int i = 0; i < 8; i++) {
        for (int j = 0; j < 26; j++) {
            char cVal = complex_function(65 + j, i + b);
            if (cVal == a[i]) {
                a[i] = ans[j];
                break; // Once found, no need to continue the loop
            }
        }
    }
}

int main() {
    char str1[] = "DHLIYJEG";
    char str2[] = "MZRERYND";
    char str3[] = "RUYODBAH";
    char str4[] = "BKEMPBRE";

    decrypt(str1, 0);
    decrypt(str2, 0x20);
    decrypt(str3, 0x40);
    decrypt(str4, 0x60);

    unsigned char key1[] = {0x0e, 0x0d, 0x7d, 0x06, 0x0f, 0x17, 0x76, 0x04};
    unsigned char key2[] = {0x6d, 0x00, 0x1b, 0x7c, 0x6c, 0x13, 0x62, 0x11};
    unsigned char key3[] = {0x1e, 0x7e, 0x06, 0x13, 0x07, 0x66, 0x0e, 0x71};
    unsigned char key4[] = {0x17, 0x14, 0x1d, 0x70, 0x79, 0x67, 0x74, 0x33};

    xor_strings(str1, key1, 8);
    xor_strings(str2, key2, 8);
    xor_strings(str3, key3, 8);
    xor_strings(str4, key4, 8);

    printf("Result: %s%s%s%s", str1,str2,str3,str4);

    return 0;
}

```
之後解密完拿回去，它印出Yes! I remember now, this is it!

就是Flag了！

（如果覺得它剛開始的字串跑太慢可以修改print_flag函式裡，local_10的值）

~~注意它印出來的G0D是0，不是O~~
```
AIS3{G0D_D4MN_4N9R_15_5UP3R_P0W3RFU1!!!}
```
## Crypto
### babyRSA
![image](https://hackmd.io/_uploads/HJMC5Q7NA.png =50%x)

先打開來看看程式！
```python
# babyRSA.py
import random
from Crypto.Util.number import getPrime
from secret import flag
def gcd(a, b):
    while b:
        a, b = b, a % b
    return a

def generate_keypair(keysize):
    p = getPrime(keysize)
    q = getPrime(keysize)
    n = p * q
    phi = (p-1) * (q-1)
    
    e = random.randrange(1, phi)
    g = gcd(e, phi)
    while g != 1:
        e = random.randrange(1, phi)
        g = gcd(e, phi)
    d = pow(e, -1, phi)
    return ((e, n), (d, n))

def encrypt(pk, plaintext):
    key, n = pk
    cipher = [pow(ord(char), key, n) for char in plaintext]
    return cipher

def decrypt(pk, ciphertext):
    key, n = pk
    plain = [chr(pow(char, key, n)) for char in ciphertext]
    return ''.join(plain)

public, private = generate_keypair(512)
encrypted_msg = encrypt(public, flag)
decrypted_msg = decrypt(private, encrypted_msg)

print("Public Key:", public)
print("Encrypted:", encrypted_msg)
# print("Decrypted:", decrypted_msg)
```
output.txt有給我們public key跟加密過後的訊息

原本是想說，可以算算看private key，但後來發現數字太大了不可能

而且最要命的是Github上也有[這個工具](https://github.com/mrdebator/BabyRSA)

後來覺得算太慢了，直接用python建立字典然後自動轉換

```python
import string

public_key = #(公鑰)

def encrypt_with_known_key(public_key, plaintext):
    key, n = public_key
    cipher = [pow(ord(char), key, n) for char in plaintext]
    return cipher

all_characters = string.ascii_letters + string.digits + string.punctuation + string.whitespace
encrypted_dict = {char: encrypt_with_known_key(public_key, char)[0] for char in all_characters}

def decrypt_with_dict(encrypted_list, encrypted_dict):
    reversed_dict = {value: key for key, value in encrypted_dict.items()}
    decrypted_text = ''.join(reversed_dict[enc_char] for enc_char in encrypted_list)
    return decrypted_text

encrypted_list = #[加密內容]

decrypted_text = decrypt_with_dict(encrypted_list, encrypted_dict)
print("Decrypted Text:", decrypted_text)
```
```
AIS3{NeverUseTheCryptographyLibraryImplementedYourSelf}
```
## Pwn
對不起，我不會；；