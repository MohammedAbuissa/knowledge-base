When using NSISCrypt, it would generate the output of any function in Chinese.
To overcome this issue we need to run it as an Ansi plugin in unicode installer which this SOF answer details: https://stackoverflow.com/a/28286741/4250244

You need the Unicode versions of the plugins when you are using `Unicode true`. If the plugin does not have a Unicode version then you should ask the plugin author to generate one.

It is also possible to call Ansi plugins from Unicode NSIS if you use the [CallAnsiPlugin plug-in](http://nsis.sourceforge.net/CallAnsiPlugin_plug-in):

```
Section

InitPluginsDir ;make sure we have $pluginsdir
	File "/ONAME=$pluginsdir\NsisCrypt.dll" "${NSISDIR}\Plugins\x86-ansi\NsisCrypt.dll" ;you must extract the Ansi plugin manually
	
	CallAnsiPlugin::Call "$pluginsdir\NsisCrypt" Hash 2 "Test string" "md5" ; The CallAnsiPlugin::Call parameters are: Dll Function ParameterCount Parameter1..N
	Pop $1
	DetailPrint MD5=$1
	
	
	CallAnsiPlugin::Call "$pluginsdir\NsisCrypt" EncryptSymmetric 4 "test string" "3des" "doq5Eh/wmT6vWoVVyRpdPhMD9KNsWa0G" "EkjR1hOing8=" 
	Pop $1
	DetailPrint 3DES=$1
	
	CallAnsiPlugin::Call "$pluginsdir\NsisCrypt" DecryptSymmetric 4 "$1" "3des" "doq5Eh/wmT6vWoVVyRpdPhMD9KNsWa0G" "EkjR1hOing8=" 
	Pop $1
	DetailPrint PlainText=$1


SectionEnd
```