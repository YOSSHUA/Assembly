# Operadores en operandos
Son palabras reservadas que solo se ejecutan durante el ensamble,no
son código ejecutable como tal.

- OFFSET - Distancia en bytes desde el 0h relativo de la memoria

- TYPE - Número de bytes de los elementos que incluye la declaración
- LENGTHOF - Cuenta el número de elementos en una declaración
- SIZEOF - Tamaño en bytes de una declaración, incluyendo todos los elementos. O bien, LENGTHOF*TYPE

<br>

# Symbols 
Son etiquetas para hacer más legible el código, pero durante el ensamble el ensamblador
sustituye el símbolo por su valor. Entonces, no reservan memoria.
<br>
<br>

# Instrucciones en ensamblador
<br>
<h2><details><summary> DumpRegs (It desplays all the general registers and the flags)</summary>
<p>

```x86asm
.CODE
CALL    DumpRegs
```
</p>
</details></h2>
</h2>

<br>
<h2><details><summary> DumpMem (It writes a range/block of memory to the console window in hexadecimal)</summary>
<p>
Pass in ESI the starting address of the block, in ECX the number of units or elements, and in EBX the unit size       (1 : byte, 2 : word, 4 : doubleword).

```x86asm
MOV     ESI, OFFSET inicio
MOV     ECX, elementos
MOV     EBX, tamDeElem                          
CALL    DumpMem
```
</p>
</details></h2>

<br>
<h2><details><summary> WriteInt (Writes a 32-bit signed integer in the console in decimal format with a leading sign and no leading zeros.)</summary>
<p>
Pass the integer into EAX.

```x86asm
MOV     EAX, valInt
CALL    WriteInt
CALL    Crlf ; salto de linea
```
</p>
</details></h2>

<br>
<h2><details><summary> WriteHex (Writes a 32-bit unsigned integer to the console window in 8-digit hexadecimal format with leading zeroes if necessary)</summary>
<p>

Pass the integer into EAX.
```x86asm
MOV     EAX, valHex
CALL    WriteHex
CALL    Crlf
```
</p>
</details></h2>

<br>
<h2><details><summary> WriteString (It writes a null-terminated string to the console window.)</summary>
<p>

Pass in EDX register the string’s offset.

```x86asm
.DATA
      line1 BYTE  “Enter the data: “, 0
 
.CODE
      MOV   EDX, OFFSET line
      CALL  WriteString
```
</p>
</details></h2>

<br>
<h2><details><summary> Crlf (It advances the cursor, inwindow console, to the beginning of the next line.)</summary>
<p>

```x86asm
call    Crlf
```
</p>
</details></h2>

<br>
<h2><details><summary> ReadInt (Reads a 32-bit signed integer from the keyboard)</summary>
<p>

Returns the value in EAX register.
Can be typed an optional leading plus or minus sign.
It sets the Overflow flag and display an error message if the value cannot be represented as  a 32-bit signed integer (-2,147,483,648 to + 2,147,483,647)

```x86asm
.DATA
     valInt SDWORD ?
 
.CODE
    CALL    ReadInt
    MOV     valInt, EAX
```
</p>
</details></h2>
	
<br>
<h2><details><summary> ReadHex (Reads a 32-bit hexadecimal integer from the keyboard)</summary>
<p>

Returns the value in EAX register.
No error checking is performed for invalid characteres.
Can use both uppercase letters and lowercase letters for the digits A through F.
```x86asm
.DATA
     valHex  DWORD ?
 
.CODE
    CALL    ReadHex
    MOV     valHex, EAX
```
</p>
</details></h2>

<br>
<h2><details><summary> ReadString (It reads a string from the keyboard, stopping when the user types the ENTER key.)</summary>
<p>

Pass the buffer’s offset in EDX register.
Set ECX to the maximum number of characters that the user can type, plus 1 to save space for the terminting null byte.
It returns, in EAX, the count of the number of characters typed by the user.

```x86asm
.DATA
      bufferR   BYTE 81 DUP(0)    ; 80 characters plus 0 (terminator)
      charCountR  DWORD ?
.CODE
      MOV       EDX, OFFSET bufferR
      MOV       ECX, 81
      CALL      ReadString
      MOV       charCountR, EAX
```
</p>
</details></h2>

<br>
<h2><details><summary> type PTR (Overrides the declared size of an operand, then allows the selection of some part of a variable)</summary>
<p>

Pass the buffer’s offset in EDX register.
Set ECX to the maximum number of characters that the user can type, plus 1 to save space for the terminting null byte.
It returns, in EAX, the count of the number of characters typed by the user.
It can also be used to combine elements of a smaller data type and move them into a larger operand.
```x86asm
.DATA
      .DATA
      myDouble DWORD 12345678h
      myBytes BYTE 12h,34h,56h,78h

.CODE
      ; From larger to smaller
      MOV EAX, myDouble
      MOV AX, WORD PTR myDouble		; loads 5678h
      MOV WORD PTR myDouble, 4A9Bh		; saves 4A9Bh
      
      ; From smaller to larger
      MOV AX, WORD PTR [myBytes]		
      MOV AX, WORD PTR [myBytes+2]		
      MOV EAX, DWORD PTR myBytes
```
</p>
</details></h2>
	  

<br>
<h2><details><summary> MUL (Multiplicación sin signo)</summary>
<p>

Usa AL, AX, EAX, RAX de forma implícita para multiplicar por el parámetro que se le de a MUL.

|Multiplicando|	Multiplicador|		Producto	|
|---|---|---|
|AL|				reg/mem8|			AH:AL = AL*reg8		|
|AX|				reg/mem16|			DX:AX = AX*reg/mem16|
|EAX|				reg/mem32|			EDX:EAX = EAX*reg/mem32|

Checa la bandera de Carry después de MUL para saber si hay bit significativos en la mitad superior del producto que se guarda en AH, DX, EDX.
El producto es del doble del tamaño.
```x86asm
.DATA
      val1 WORD 2000h
      val2 WORD 100h

.CODE

      MOV AX,val1
      MUL val2	; DX:AX = 00200000h, CF=1

      MOV EAX,12345h
      MOV EBX,1000h
      MUL EBX	; EDX:EAX = 0000000012345000h, CF=0

```
</p>
</details></h2>

<br>
<h2><details><summary> IMUL (Multiplicación con signo)</summary>
<p>

Usa AL, AX, EAX, RAX de forma implícita para multiplicar por el parámetro que se le de a IMUL.
Preserva el signo del producto usando extensión de signo en la mitad superior.
CF y OF están en 1 si la mitad superior del producto NO es una extensión de signo de la mitad inferior.

|Multiplicando	|Multiplicador|		Producto	|
|---|---|---|
|AL		|		reg/mem8		|	AH:AL = AL*reg8	|	
|AX		|		reg/mem16		|	DX:AX = AX*reg/mem16|
|EAX		|		reg/mem32		|	EDX:EAX = EAX*reg/mem32|
</p>
</details></h2>

<br>
<h2><details><summary> IMUL (Multiplicación con signo de dos operandos)</summary>
<p>
Preserva el signo del producto usando extensión de signo en la mitad superior.
CF y OF están en 1 si los dígitos significativos se pierden.

IMUL reg16,reg/mem16
IMUL reg16,imm8
IMUL reg16,imm16

IMUL reg32,reg/mem32
IMUL reg32,imm8
IMUL reg32,imm32

|Multiplicando	|Multiplicador	|	Producto	|
|---|---|---|
|reg16	|		reg/mem16	|		reg16 = reg16*reg/mem16/imm8/imm16|
|reg32	|		reg/mem32	|		reg32 = reg32*reg/mem32/imm32/imm32


</p>
</details></h2>

<br>
<h2><details><summary> IMUL (Multiplicación con signo de tres operandos)</summary>
<p>
El resultado se guarda en el primer operando.
CF y OF están en 1 si los dígitos significativos se pierden.
El resultado se guarda en el primer registro.
Three-operand 16-bit formats:
IMUL reg16, reg/mem16, imm8
IMUL reg16, reg/mem16, imm16

Three-operand 32-bit formats:
IMUL reg32, reg/mem32, imm8
IMUL reg32, reg/mem32, imm32



</p>
</details></h2>

<br>
<h2><details><summary> DIV (División sin signo)</summary>
<p>

Solo se da un operando que funge como el divisor.

DIV reg/mem8    
DIV reg/mem16  <br>
DIV reg/mem32  <br>
DIV reg/mem64  <br>


|Dividendo |Divisor | Cociente |Reminder|
|---|---|---|---|
|AH:AL | reg/mem8 | AL | AH|
|DX:AX | reg/mem16| AX | DX|
|EDX:EAX | reg/mem32| EAX | EDX|


Limpiar reminder, cargar el diviendo en AL, AX, EAX y el número que lo va a dividir en AL, AX o EAX.

Todas las banderas quedan indefinidas.

Si el cociente no cabe en el destino o el divisor es 0 se interrumple el CPU y el programa se detiene.

</p>
</details></h2>

<br>
<h2><details><summary> IDIV (División con signo)</summary>
<p>

Se debe hacer la extensión de signo antes de que se haga la división. Se llena el Hight byte/word/doubleword
con el bit de signo.

Atajos:
The CBW, CWD, and CDQ instructions provide important sign-extension operations:
-	CBW (convert byte to word) extends AL into AH
- 	CWD (convert word to dword) extends AX into DX
- 	CDQ (convert dword to qword) extends EAX into EDX
- 	CQO (convert qword to oword) extends RAX into RDX

|Dividendo |Divisor | Cociente |Reminder|
|---|---|---|---|
|AH:AL | reg/mem8 | AL | AH|
|DX:AX | reg/mem16| AX | DX|
|EDX:EAX | reg/mem32| EAX | EDX|


Limpiar reminder, cargar el diviendo en AL, AX, EAX y el número que lo va a dividir en AL, AX o EAX.

Todas las banderas quedan indefinidas.

Si el cociente no cabe en el destino o el divisor es 0 se interrumple el CPU y el programa se detiene.

</p>
</details></h2>
<br/>

<h2><details><summary> PUSH/POP</summary>
<p>

Push guarda temporalmente en el stack el valor del registro que se le pase.
Pop borra del stack lo que se guardó con push y recupera en el registro lo que se había gurdado.

```x86asm

PUSH reg

POP reg

```


</p>
</details></h2>
<br/>


# Instrucciones de transferencia de control (jumps)

<h2><details><summary> JMP(Da un salto a la etiqueta especificada) </summary>
<p>

```x86asm

.CODE
	 JMP lbl3
	 instrucR
	 instrucT
lbl3:
	...
```

</p>
</details></h2>
<br/>

<h2><details><summary> Jcond Based on one Flag </summary>
<p>

![image](https://user-images.githubusercontent.com/42878951/138994603-0218aef6-5e2a-4b3d-9c70-340ada08d981.png)

</p>
</details></h2>
<br/>


<h2><details><summary> CMP </summary>
<p>

La lógica es hacer leftOp - rightOp. Afecta todas las banderas.
	
CMP reg, reg
	
CMP reg, mem
	
CMP reg, imm
	
CMP mem, reg
	
CMP mem, imm
	
```x86asm

.CODE
	 CMP leftOp rightOp 
```

</p>
</details></h2>
<br/>


<h2><details><summary> Jcond para igualdad </summary>
<p>

Funcionada para signado o sin signo.
![image](https://user-images.githubusercontent.com/42878951/138995091-50d1861c-86ed-432f-953e-804d2eab3e1b.png)

	
</p>
</details></h2>
<br/>


<h2><details><summary> Jcond unsigned </summary>
<p>

![image](https://user-images.githubusercontent.com/42878951/138995158-67edd185-faaa-4963-9250-211d19941c35.png)
	
</p>
</details></h2>
<br/>



<h2><details><summary> Jcond signed </summary>
<p>

![image](https://user-images.githubusercontent.com/42878951/138995211-348531ae-416d-495c-b637-275eb18a525b.png)
	
</p>
</details></h2>
<br/>


<h2><details><summary> AND </summary>
<p>

Es un bitwise operator. Modifies Sign, Zero, and Parity.
	
Sintaxis: AND destination, source
	
Opciones permitidas
	
AND reg, reg
	
AND reg, mem
	
AND reg, imm
	
AND mem, reg
	
AND mem, imm

</p>
</details></h2>
<br/>

<h2><details><summary> OR </summary>
<p>

Es un bitwise operator. Modifies Sign, Zero, and Parity.
	
Sintaxis: OR destination, source
	
Opciones permitidas
	
OR reg, reg
	
OR reg, mem
	
OR reg, imm
	
OR mem, reg
	
OR mem, imm

</p>
</details></h2>
<br/>

<h2><details><summary> OR </summary>
<p>

Es un bitwise operator. With 0 retains its value, with 1 reverses value. Modifies Sign, Zero, and Parity.
	
Sintaxis: XOR destination, source
	
Opciones permitidas
	
XOR reg, reg
	
XOR reg, mem
	
XOR reg, imm
	
XOR mem, reg
	
XOR mem, imm

</p>
</details></h2>
<br/>

<h2><details><summary> NOT </summary>
<p>

No flags are affected. Opciones:
	
NOT reg
	
NOT mem
	
</p>
</details></h2>
<br/>

<h2><details><summary> TEST (nondestructive AND)</summary>
<p>

Modifies the Sign, Zero, and Parity flags.
	
</p>
</details></h2>
<br/>


# MACRO

Es un bloque de código con nombre.
Durante el pre-procesamiento del ensamblador a lenguaje máquina se susyituye el nombre del macro con el fragmento de código que representa. Es una utilería para simplificar el código.

```x86asm
;Incluir librería de macros
INCLUDE \masm32\Irvine\Macros.inc

nameMacro MACRO [arg1, arg2, ...]
```

# Condicionales
