
Link: [Brutales transiciones entre páginas con CSS (0 JavaScript)](https://www.youtube.com/watch?v=5L4M4-WOGwo)
Tags: #css 

Conseguir transiciones sin javascript para la #css/navegacion entre página. Transiciones fade-in, fade-out desde una imagen o desde el contenido completo

### Transicion básica
```
@view-transition{
	navigation:auto
}
```
Con este código podemos ya definir una #css/transicion entre páginas
```
:view-transition-group(*){
	animation-duration: 1s;
}
```
Duración de la animación

### Definir animación: Slide
```
::view-transition-old(root){
	animation:slide-out-to-left 0.5s forwards;
}

::view-transition-new(root){
	animation:slide-in-from-rigth 0.5s forwards;
}
```
Con este código podemos la animación de la #css/transicion/slide entre páginas
```
@keyframes slide-out-to-left{
	from:{
		transform: translateX(0); /*Indicación que desde la posición 0 ...*/
	}
	to:{
		transform: translateX(-100%); /*Se corra su totalidad hacia la izquierda*/
	}
}

@keyframes slide-in-from-rigth{
	from:{
		transform: translateX(100%); /*Indicación que desde la posición de la izquierda (oculto digamos) ...*/
	}
	to:{
		transform: translateX(0%); /*Se corra hacia la derecha*/
	}
}
```
Linkear desde una #css/transicion/imagen esta transición, hay que poner el estilo directamente en el html del origen y en la pagina del destino

<html lang="es">
	<head>
		<meta charset="UTF-8">
		<title>Origen</title>
	</head>
	<body>
		<div class="imagen-ejemplo">
			<a href="#destino">
				<img src="image-1.png" alt="ejemplo-origen" style="view-transition-name: imagen-01"/>
			</a>
		</div>
	</body>
</html>

<html lang="es">
	<head>
		<meta charset="UTF-8">
		<title>Destino</title>
	</head>
	<body>
		<a href="#origen">Volver</a>
		<div class="imagen-ejemplo">
				<img src="image-1.png" alt="ejemplo-destion" style="view-transition-name: imagen-01"/>
		</div>
	</body>
</html>


