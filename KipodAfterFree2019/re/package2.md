Package2
===
#### [re, 20p, 27 solves]
**TLDR**; obfuscated .Net binary which was decrypting hardcoded, encrypted password with hardcoded key. I used avaliable deobfuscator, did some reversing, found encypted password and hardcoded key and wrote simple python script that decrypted it.    

To decompile this binary I used dnSpy. When I loaded it to the decompiler I saw that function and variables names are obfustaced and consists of unicode characters like `\u200C\u200B\u202E\u206C\u202C\u202B\u202D...`. After looking around for a bit I found interesting thing in binary metadata:
```c#
// C:\Users\bilith\Package2.exe
// WpfApp1.exe

// Global type: <Module>
// Entry point: <Module>.\u206B\u202E\u206D\u206E\u200D\u200D\u202A\u202D\u202A\u200C\u206F\u206B\u206A\u202A\u206C\u206B\u206B\u200D\u202A\u200C\u200C\u206D\u200B\u206C\u202C\u206D\u206E\u206D\u202A\u202B\u202E\u200B\u206A\u200C\u200B\u200B\u202D\u200D\u206E\u202A\u202E
// Architecture: AnyCPU (32-bit preferred)
// Runtime: .NET Framework 4.7.2
// Timestamp: 5DF7894A (12/16/2019 5:40:26 AM)

using System;
using System.Runtime.CompilerServices;

[module: SuppressIldasm]
[module: ConfusedBy("ConfuserEx v1.0.0")]
```
After some research I found out that ConfuserEx is an obfuscator/protector for .Net binaries. So I started looking for deobfuscator. I googled for `ConfuserEx unpacker` and got couple results, however the first one I tried, crashed during deobsucation. I finally found [working one](https://github.com/XenocodeRCE/ConfuserEx-Unpacker) on tuts4you forum and I was able to slightly clean this binary.
```powershell
C:\Users\bilith\1.0>"ConfuserEx Dynamic Unpacker.exe" -s Package22.exe
Yeah confuserex unpacker so what
[!] Anti Tamper Detected
[!] Anti Tamper Removed Successfully
[!] Compressor Detected
[!] Compressor Removed Successfully
[!] Now Cleaning The koi Module
[!] Anti Tamper Detected
[!] Anti Tamper Removed Successfully
[!] Cleaning Proxy Calls
[!] Amount Of Proxy Calls Fixed: 58
[!] Decrytping Strings
[!] Amount Of Strings Decrypted: 12
C:\Users\bilith\1.0>
```
After that I was able to see more modules or classes (not sure about that term). After looking throught still kinda obfuscated code I found interesting part (I removed not relevant code for better readability):
```c#
public class nKYbeWzkWqgYIelUsfuLWBeKzhqjA : Window, IComponentConnector
{
	public nKYbeWzkWqgYIelUsfuLWBeKzhqjA()
	{
		this.InitializeComponent();
		new Button
		{
			Name = "btn1"
		}.Click += this.\u206C\u206A\u202A\u200B\u206E\u200C\u200D\u200F\u202B\u202D\u200F\u202E\u206C\u206D\u206C\u200C\u202C\u206D\u200D\u206B\u200B\u200B\u200D\u200B\u202A\u202D\u200B\u202E\u200F\u200E\u202C\u202A\u206D\u206E\u202A\u206D\u202D\u206B\u206F\u202C\u202E;
	}

	private string \u206F\u206A\u202B\u202B\u202D\u200C\u202B\u200F\u202E\u200D\u200D\u206D\u200C\u206B\u202D\u202E\u206E\u206F\u206D\u202A\u202B\u200F\u206F\u206F\u200F\u200E\u206F\u206A\u206E\u202A\u200C\u202D\u202B\u202B\u202C\u206A\u200D\u200B\u202A\u202A\u202E(string A_1, string A_2)
	{
		byte[] array = Convert.FromBase64String(A_1);
		TripleDESCryptoServiceProvider tripleDESCryptoServiceProvider = new TripleDESCryptoServiceProvider();
		tripleDESCryptoServiceProvider.Key = Encoding.UTF8.GetBytes(A_2);
		tripleDESCryptoServiceProvider.Mode = CipherMode.ECB;
		tripleDESCryptoServiceProvider.Padding = PaddingMode.PKCS7;
		byte[] bytes = tripleDESCryptoServiceProvider.CreateDecryptor().TransformFinalBlock(array, 0, array.Length);
		tripleDESCryptoServiceProvider.Clear();
		return Encoding.UTF8.GetString(bytes);
	}
 
	private void \u206C\u206A\u202A\u200B\u206E\u200C\u200D\u200F\u202B\u202D\u200F\u202E\u206C\u206D\u206C\u200C\u202C\u206D\u200D\u206B\u200B\u200B\u200D\u200B\u202A\u202D\u200B\u202E\u200F\u200E\u202C\u202A\u206D\u206E\u202A\u206D\u202D\u206B\u206F\u202C\u202E(object A_1, RoutedEventArgs A_2)
	{
		for (int num = 138344687; num == 138344687; num = 397736)
		{
		}
		string text = this.\u200E\u200F\u206D\u200F\u206D\u206D\u206D\u206F\u200C\u200E\u206A\u202B\u200E\u200E\u206B\u200F\u206A\u206B\u200D\u206F\u200C\u206C\u206E\u200F\u200F\u202C\u202C\u202A\u206A\u200C\u206D\u200D\u206F\u206E\u202E\u200F\u200F\u202A\u202D\u206A\u202E.Text;
		string text2 = this.\u202D\u202E\u200C\u202B\u202A\u202D\u206F\u200B\u206D\u206E\u206F\u202C\u206B\u202E\u200B\u202B\u206D\u200D\u200E\u206E\u206E\u200C\u200B\u206B\u206B\u206B\u200E\u200C\u200D\u202C\u200C\u206A\u202A\u206B\u206A\u206A\u202B\u206A\u200C\u206B\u202E.Text;
		this.\u200F\u200E\u202D\u200C\u206B\u202B\u200F\u206B\u206F\u202A\u202B\u202C\u206B\u200E\u206A\u206B\u200C\u206B\u206A\u200F\u202B\u206E\u206D\u200F\u200C\u202C\u202D\u200B\u202D\u200C\u202A\u202E\u200D\u202A\u206F\u202B\u202D\u200F\u202B\u200F\u202E(text);
	}

	//(...)

	private void \u202A\u202C\u206A\u202C\u200C\u200D\u200D\u200F\u200B\u206A\u202B\u202C\u200F\u206C\u202B\u206B\u200C\u206F\u202A\u202B\u200F\u206A\u206E\u200B\u200C\u206F\u206F\u206F\u206A\u200B\u206F\u200E\u202B\u202A\u200E\u202D\u200E\u206C\u206B\u202E(string A_1)
	{
		if (A_1 == this.\u206F\u206A\u202B\u202B\u202D\u200C\u202B\u200F\u202E\u200D\u200D\u206D\u200C\u206B\u202D\u202E\u206E\u206F\u206D\u202A\u202B\u200F\u206F\u206F\u200F\u200E\u206F\u206A\u206E\u202A\u200C\u202D\u202B\u202B\u202C\u206A\u200D\u200B\u202A\u202A\u202E(this.\u200F\u202A\u202A\u200C\u200F\u206C\u202B\u202C\u206B\u206B\u200D\u206A\u202D\u200B\u202C\u202B\u202B\u200B\u202C\u202B\u202E\u202E\u202A\u202D\u200C\u202B\u206F\u202C\u202C\u206D\u206E\u206B\u206D\u200D\u202D\u202D\u200E\u202B\u200D\u200E\u202E, this.\u206B\u206A\u200E\u200C\u206D\u200B\u200D\u200F\u202C\u206C\u202B\u206E\u206D\u200D\u200C\u200D\u202D\u200D\u202A\u202E\u206D\u202D\u200C\u202A\u206F\u206D\u200B\u206B\u202A\u206F\u206C\u200E\u202D\u202E\u200F\u200E\u206A\u200F\u200F\u202E\u202E))
		{
			MessageBox.Show("Enjoy your package!");
			return;
		}
		MessageBox.Show("Thats not your package!");
	}

	// (...)

	private string \u200F\u202A\u202A\u200C\u200F\u206C\u202B\u202C\u206B\u206B\u200D\u206A\u202D\u200B\u202C\u202B\u202B\u200B\u202C\u202B\u202E\u202E\u202A\u202D\u200C\u202B\u206F\u202C\u202C\u206D\u206E\u206B\u206D\u200D\u202D\u202D\u200E\u202B\u200D\u200E\u202E = "yTzdlwehExU13XhHfGxExaLthEFOSRPuy8GxBZVvPig=";

	private string \u206B\u206A\u200E\u200C\u206D\u200B\u200D\u200F\u202C\u206C\u202B\u206E\u206D\u200D\u200C\u200D\u202D\u200D\u202A\u202E\u206D\u202D\u200C\u202A\u206F\u206D\u200B\u206B\u202A\u206F\u206C\u200E\u202D\u202E\u200F\u200E\u206A\u200F\u200F\u202E\u202E = "vyt4-4nrz-1337ko";

// (...)
```
That's the class that handles buttons actions and user provided data. After a little bit of reversing I knew more less what's going on (again some parts were omitted):
```c#
public class MainWindow : Window, IComponentConnector
{
	public MainWindow()
	{
		this.InitializeComponent();
		new Button
		{
			Name = "btn1"
		}.Click += this.btn1Action;
	}

	private string Decrypt(string A_1, string A_2)
	{
		byte[] array = Convert.FromBase64String(A_1);
		TripleDESCryptoServiceProvider tripleDESCryptoServiceProvider = new TripleDESCryptoServiceProvider();
		tripleDESCryptoServiceProvider.Key = Encoding.UTF8.GetBytes(A_2);
		tripleDESCryptoServiceProvider.Mode = CipherMode.ECB;
		tripleDESCryptoServiceProvider.Padding = PaddingMode.PKCS7;
		byte[] bytes = tripleDESCryptoServiceProvider.CreateDecryptor().TransformFinalBlock(array, 0, array.Length);
		tripleDESCryptoServiceProvider.Clear();
		return Encoding.UTF8.GetString(bytes);
	}

	private void btn1Action(object A_1, RoutedEventArgs A_2)
	{
		for (int num = 138344687; num == 138344687; num = 397736)
		{
		}
		string text = this.passwordField.Text;
		string text2 = this.loginField.Text;
		this.CheckPassoword1(text);
	}

	private void CheckPassoword1(string A_1)
	{
		this.CheckPassword2(A_1);
	}

	private void CheckPassword2(string A_1)
	{
		this.CheckPassword(A_1);
	}

	private void CheckPassword(string A_1)
	{
		if (A_1 == this.Decrypt(this.password_b64, this.key))
		{
			MessageBox.Show("Enjoy your package!");
			return;
		}
		MessageBox.Show("Thats not your package!");
	}

 // (...)

	// (...)
 
	}

	private string password_b64 = "yTzdlwehExU13XhHfGxExaLthEFOSRPuy8GxBZVvPig=";

	private string key = "vyt4-4nrz-1337ko";

	internal TextBox loginField;

	internal TextBox passwordField;

	internal Button confirmBUtton;

	private bool \u202E\u202D\u206E\u206F\u206C\u200F\u202D\u202C\u206C\u200D\u200C\u200D\u206D\u206F\u206B\u200B\u206A\u206E\u200F\u200E\u206F\u206B\u206C\u206C\u206C\u206F\u206B\u200C\u200B\u202A\u206E\u202D\u200F\u202B\u200F\u200C\u200B\u202A\u202D\u200E\u202E;
}
```
Function btn1Action is ran whenever user clicks "Take Package!" button . All it does is save user provided data and call function that check provided password. Function CheckPassword decrypt hardcoded password and compare it with the password provided by the user. From the Decrypt function we can clearly see that password is encryped with 3DES in ECB mode and Base64 encoded. To decrypt it I wrote python script:
```python
#!/usr/bin/python3
from Crypto.Cipher import DES3
import base64


enc_pass = base64.b64decode('yTzdlwehExU13XhHfGxExaLthEFOSRPuy8GxBZVvPig=')
key = b'vyt4-4nrz-1337ko'
dec = DES3.new(key, DES3.MODE_ECB)

print(dec.decrypt(enc_pass).decode('utf-8'))
```
Output: `KAF{p0rch_thi3fs_g0_4way}`
