## LECCION 1
Antes de que podamos programar la parte funcional del juego, necesitamos crear la estructura básica de la página que lo va a contener. Podemos hacerlo utilizando HTML y el elemento  <canvas> .

<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>Breakout</title>
    <style>* { padding: 0; margin: 0; } canvas { background: #000000; display: block; margin: 0 auto; }</style>
  
// En este caso hemos definido el titulo del documento html como "Breaout", pero se puede ajustar al tamaño que requiera.  
// El color del fondo "background" esta definido como #000000 en hexadecimal pero no es necesario que sea este el color por defecto.

</head>
<body>

<canvas id="myCanvas" width="480" height="320"></canvas>

// En este caso hemos definido el tamaño del lienzo "canvas" con un ancho de 480 px y un alto de 320, pero se puede ajustar al tamaño que requiera.

<script>
var canvas = document.getElementById("myCanvas");
var ctx = canvas.getContext("2d");
  
// El codigo en JavaScript va a ir aqui, ya que canvas es solo el lienzo quien dibuja es javascript por lo que es necesario llamar al elemento canvas desde el script y definir en este caso el contexto 2d.

</script>

</body>
</html>

## LECCION 2
Ahora vamos a dibujar una pelota y a hacer que se mueva. Técnicamente, estaremos pintando la pelota en la pantalla, borrándola y luego pintándola de nuevo en una posición ligeramente diferente cada fotograma para dar la impresión de movimiento, igual que se hace en las películas.(De aqui en adelante solo se mostrara el codigo en javascript ya que la parte previa no la volveremos a editar).

<script>
    var canvas = document.getElementById("myCanvas");
    var ctx = canvas.getContext("2d");
  
    // A continuacion se definen las variables de la pelota, las variables "x" "y" define donde estara el centro de la pelota y "dx"    "dy" definen la magnitud y direccion del movimiento.
  
    var x = canvas.width/2;
    var y = canvas.height-30;
    var dx = 2;
    var dy = -2;
    
    // Seguido a esto se crea la funcion "drawball" la cual dibujara un circulo ya que utilizaremos el contexto "arc", esta funcion tiene en sus dos primeras variables las coordenadas del centro de la circunferencia, el tercer argumento es el radio de la esfera, y los ultimos dos son los angulos donde inicia y termina (estos angulos se deben poner en radianes), ademas de esto se puede definir el contexto "fillStyle" para rellenar la figura con un color o "strokeStyle" para solo colorear su contorno.
    
    function drawBall() {
        ctx.beginPath();
        ctx.arc(x, y, 10, 0, Math.PI*2);
        ctx.fillStyle = "#0095DD";
        ctx.fill();
        ctx.closePath();
    }
    
    // Ahora vamos ha hacer que se dibuje cada 10 milisegundos pero que antes de esto nos limpie el lienzo con el objetivo de crear la animacion, ya que de lo contrario solo trazara una linea con el grosor del radio del circulo.

    function draw() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        drawBall();
        x += dx;
        y += dy;
    }
    
    setInterval(draw, 10);
</script>

## LECCION 3
Continuando con el desarrollo crearemos una funcion para detectar la colisión de la pelota con la pared y si es así, cambiaremos la dirección de su movimiento en consecuencia, se debe definir una variable llamada ballRadius para facilitar los cálculos.

<script>
    var canvas = document.getElementById("myCanvas");
    var ctx = canvas.getContext("2d");
    var ballRadius = 10;
    var x = canvas.width/2;
    var y = canvas.height-30;
    var dx = 2;
    var dy = -2;
    function drawBall() {
        ctx.beginPath();
        ctx.arc(x, y, ballRadius, 0, Math.PI*2);
        ctx.fillStyle = "#0095DD";
        ctx.fill();
        ctx.closePath();
    }
    function draw() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        drawBall();
  
        // Esta es la parte del codigo que añade la deteccion de las colisiones en la funcion "Draw"
        if(x + dx > canvas.width-ballRadius || x + dx < ballRadius) {
            dx = -dx;
        }
        if(y + dy > canvas.height-ballRadius || y + dy < ballRadius) {
            dy = -dy;
        }
        
        x += dx;
        y += dy;
    }
    setInterval(draw, 10);
</script>

## LECCION 4

## LECCION 5
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>Breakout</title>
    <style>* { padding: 0; margin: 0; } canvas { background: #000000; display: block; margin: 0 auto; }</style>
</head>
<body>

<canvas id="myCanvas" width="480" height="320"></canvas>

<script>
var canvas = document.getElementById("myCanvas");
var ctx = canvas.getContext("2d");
var ballRadius = 10;
var ballColor = "#FEDF00";
var x = canvas.width/2;
var y = canvas.height-30;
var dx = 2;
var dy = -2;
var paddleHeight = 10;
var paddleWidth = 75;
var paddleX = (canvas.width-paddleWidth)/2;
var rightPressed = false;
var leftPressed = false;
var brickRowCount = 5;
var brickColumnCount = 3;
var brickWidth = 75;
var brickHeight = 20;
var brickPadding = 10;
var brickOffsetTop = 30;
var brickOffsetLeft = 30;
var score = 0;
var lives = 3;
var bricks = [];
for(c=0; c<brickColumnCount; c++) {
    bricks[c] = [];
    for(r=0; r<brickRowCount; r++) {
        bricks[c][r] = { x: 0, y: 0, status: 1 };
    }
}
document.addEventListener("keydown", keyDownHandler, false);
document.addEventListener("keyup", keyUpHandler, false);
document.addEventListener("mousemove", mouseMoveHandler, false);
function keyDownHandler(e) {
    if(e.keyCode == 39) {
        rightPressed = true;
    }
    else if(e.keyCode == 37) {
        leftPressed = true;
    }
}
function keyUpHandler(e) {
    if(e.keyCode == 39) {
        rightPressed = false;
    }
    else if(e.keyCode == 37) {
        leftPressed = false;
    }
}
function mouseMoveHandler(e) {
    var relativeX = e.clientX - canvas.offsetLeft;
    if(relativeX > 0 && relativeX < canvas.width) {
        paddleX = relativeX - paddleWidth/2;
    }
}
function collisionDetection() {
    for(c=0; c<brickColumnCount; c++) {
        for(r=0; r<brickRowCount; r++) {
            var b = bricks[c][r];
            if(b.status == 1) {
                if(x > b.x && x < b.x+brickWidth && y > b.y && y < b.y+brickHeight) {
                    dy = -dy;
                    b.status = 0;
                    paddleWidth = paddleWidth * 1.1;
                    score++;
                    if(score == brickRowCount*brickColumnCount) {
                        alert("¡Haz ganado, Felicidades!");
                        document.location.reload();
                    }
                }
            }
        }
    }
}
function drawBall() {
    ctx.beginPath();
    ctx.arc(x, y, ballRadius, 0, Math.PI*2);
    ctx.fillStyle = ballColor;
    ctx.fill();
    ctx.closePath();
}
function drawPaddle() {
    ctx.beginPath();
    ctx.rect(paddleX, canvas.height-paddleHeight, paddleWidth, paddleHeight);
    ctx.fillStyle = "#FEDF00";
    ctx.fill();
    ctx.closePath();
}
function drawBricks() {
    for(c=0; c<brickColumnCount; c++) {
        for(r=0; r<brickRowCount; r++) {
            if(bricks[c][r].status == 1) {
                var brickX = (r*(brickWidth+brickPadding))+brickOffsetLeft;
                var brickY = (c*(brickHeight+brickPadding))+brickOffsetTop;
                bricks[c][r].x = brickX;
                bricks[c][r].y = brickY;
                ctx.beginPath();
                ctx.rect(brickX, brickY, brickWidth, brickHeight);
                ctx.fillStyle = "#FEDF00";
                ctx.fill();
                ctx.closePath();
            }
        }
    }
}
function drawScore() {
    ctx.font = "16px Arial";
    ctx.fillStyle = "#FEDF00";
    ctx.fillText("Puntaje: "+score, 8, 20);
}
function drawLives() {
    ctx.font = "16px Arial";
    ctx.fillStyle = "#FEDF00";
    ctx.fillText("Vidas: "+lives, canvas.width-65, 20);
}
function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    drawBricks();
    drawBall();
    drawPaddle();
    drawScore();
    drawLives();
    collisionDetection();
    
    if(x + dx > canvas.width-ballRadius || x + dx < ballRadius) {
        dx = -dx;
    }
    if(y + dy < ballRadius) {
        dy = -dy;
        paddleWidth = paddleWidth * 0.8;
    }
    else if(y + dy > canvas.height-ballRadius) {
        if(x > paddleX && x < paddleX + paddleWidth) {
            dy = -dy;
            paddleWidth = paddleWidth * 0.8;
        }
        else {
            lives--;
            if(!lives) {
                alert("¡Haz perdido, Intentalo de nuevo!");
                document.location.reload();
            }
            else {
                x = canvas.width/2;
                y = canvas.height-30;
                dx = 3;
                dy = -3;
                paddleX = (canvas.width-paddleWidth)/2;
                paddleWidth = 75;
            }
        }
    }
    
    if(rightPressed && paddleX < canvas.width-paddleWidth) {
        paddleX += 7;
    }
    else if(leftPressed && paddleX > 0) {
        paddleX -= 7;
    }
    
    x += dx;
    y += dy; 
    requestAnimationFrame(draw);
}
draw();
</script>

</body>
</html>
