; Universidad del Valle de Guatemala
; Josue Say 22801		Gustavo Cruz 22779
;Descripción: El programa califica al cliente para comprobar si cumple los requerimentos para que su credito sea aprobado

section .data
	nombre 				db "Samuel ", 0 ;al colocar 0, e imprimir hace salto de línea
	apellido 			db "Palancas ", 0
	edad 				dw 19
	edadS				db "19",0
	ingresos 			dw 10000
	ingresosS			db "10000",0
	mesescont 			dw 18
	mesescontS			db "18",0
	mesesmin			db "Meses de continuidad laboral ",0
	montop 				dw 15000
	montopS				db "15000",0
	rq					dw 0
	calificacion 		db "A", 0
	calificacionmin 	db "A", 0
	msg1 				db " Califica para el credito", 0
	msg2 				db " No califica para el credito", 0
	msg3 				db " OK", 0
	msgedad				db "Edad: ", 0
	msgingresos			db "Ingresos ", 0
	msgcalifica			db "Calificación ", 0

section .text
	global _start:
;No puedo utilizar IF, ELSE, ELSEIF por lo que utilizaré los salto de linea y la comparación para solucionarlo

_start:
    ; se imprime el nombre
    mov eax, 4
    mov ebx, 1
    mov ecx, nombre
    mov edx, 7
    int 0x80

    ; se imprime el apellido
    mov eax, 4
    mov ebx, 1
    mov ecx, apellido
    mov edx, 9
    int 0x80

    jmp veredad
veredad:
    ; se imprime la palabra edad
    mov eax, 4
    mov ebx, 1
    mov ecx, msgedad
    mov edx, 6
    int 0x80

    ; se imprime el valor de edad
    mov eax, 4
    mov ebx, 1
    mov ecx, edadS
    mov edx, 2
    int 0x80

    ; se comprueba si la edad es mayor a 18 años
    mov ax, [edad]
    cmp ax, 18
    jmp veringresos
    jge OK ; salta si la edad es mayor o igual a 18 años

veringresos:
    ; se imprime la palabra INGRESOS
    mov eax, 4
    mov ebx, 1
    mov ecx, msgingresos
    mov edx, 10
    int 0x80

    ; se imprime el valor de ingreso
    mov eax, 4
    mov ebx, 1
    mov ecx, ingresosS
    mov edx, 5
    int 0x80

    ; se imprime el valor del monto solicitado
    mov eax, 4
    mov ebx, 1
    mov ecx, montopS
    mov edx, 5
    int 0x80

    ; se hace la comparación entre ingresos y monto
    mov ax, [ingresos]
    mov bx, [montop]
    cmp bx, ax
    jle OK

    jmp vermes
vermes:
    ; se imprime la palabra MESES DE CONTINUIDAD LABORAL
    mov eax, 4
    mov ebx, 1
    mov ecx, mesesmin
    mov edx, 29
    int 0x80

    ; se imprime el valor de los meses de continuidad laboral
    mov eax, 4
    mov ebx, 1
    mov ecx, mesescontS
    mov edx, 3
    int 0x80

    ; se hace la comparación de los meses de continuidad laboral y los meses mínimos
    mov ax, [mesescont]
    mov bx, 12
    cmp ax, bx
    jge OK

    jmp vercal

vercal:
    ; se imprime el título de calificación
    mov eax, 4
    mov ebx, 1
    mov ecx, msgcalifica
    mov edx, 13
    int 0x80

    ; se imprime la calificacion
    mov eax, 4
    mov ebx, 1
    mov ecx, calificacion
    mov edx, 1
    int 0x80

    ; se imprime la calificacion mínima
    mov eax, 4
    mov ebx, 1
    mov ecx, calificacionmin
    mov edx, 1
    int 0x80

    ; se hace la comprobación de la calificación de Super Intendencia de Bancos
    mov esi, calificacion
    mov edi, calificacionmin
    lodsb
    lodsb
    cmp al, bl
    je OK

    ; se hace la verificación sobre los requisitos cumplidos
    mov ax, [rq]
    mov bx, 4
    cmp ax, bx
    je aprobado
    jne noaprobado

    ; Es necesario indicar la salida del programa en NASM
    ; Este label me permite imprimir OK para verificar que se cumple el requisito
OK:
    ; deseo llevar un conteo de las veces que la persona cumple los requisitos
    ; de esta forma puedo comprobar si todos los requerimentos se cumplen
    ; y se aprueba el préstamo
    mov dx, [rq]
    inc dx
    mov [rq], dx

    mov eax, 4
    mov ebx, 1
    mov ecx, msg3 ; imprime OK
    mov edx, 3 ; longitud del mensaje, en este caso el mensaje es OK son 3
    int 0x80



aprobado:
    ; Imprime que el crédito ha sido aprobado
    mov eax, 4
    mov ebx, 1
    mov ecx, msg1
    mov edx, 25
    int 0x80

    ; Salto a la salida del programa
    jmp exit

noaprobado:
     ; Imprime que el crédito no ha sido aprobado
    mov eax, 4
    mov ebx, 1
    mov ecx, msg2
    mov edx, 28
    int 0x80

    ; Salto a la salida del programa
    jmp exit

exit:
    ; Salida del programa
    mov eax, 1
    xor ebx, ebx
    int 0x80
