# ProyectoTermo
- Proyecto final modelos termodinámicos <br>
- <b>Universidad Panamericana</b> <br>

<b>Autores:</b> <br>
- Rafael Patino
- Ramón Ruiz

## Documentación

### proyecto.html
Es el archivo principal y está dividido en dos secciones, primero consiste de toda la interfaz, incluyendo la gráfica, ingreso de parámetros y la base para la tabla de resultados. <br><br>


La siguiente función se encarga de actualizar la gráfica
``` Javascript
chart.updateSeries([{
  data: mydata
}]);
```
La función start() se llama cuando es oprimido el botón de comenzar.
``` Javascript
function start() {
  flag++;

```
Se accede a los valores que el usuario ingresó mediante la interfaz y se hace el cálculo de la temp sat, así como hfg.
``` Javascript
  var ti = parseFloat(document.getElementById("ti").value);
  var mi = parseFloat(document.getElementById("mi").value);
  var potencia = parseFloat(document.getElementById("potencia").value);
  var p_atm = parseFloat(document.getElementById("p_atm").value);
  var intervalo = parseFloat(document.getElementById("intervalo").value);
  var tf = 0;
  var tsat = Math.pow(((1/373.14)-(0.00874*Math.log(p_atm/101325)/40.66)),-1) - 273.15;
  var h_fg = 2256.4 * Math.pow(((1-((tsat+273.15)/647.096))/(1-0.57665623)),0.375);
  var mf = 0;
  temp = ti;

```
Aquí se inicializa la función de actualizado constante (para nutrir la gráfica y la tabla). <br>
Contamos con una bandera que nos indica si debemos seguir graficando y un condicional para escoger Calor sensible o Calor latente. <br><br>
De esta manera, consideramos que para calor sensible (Qs) la siguiente fórmula es útil, puesto que nos permite conocer la variación de temperatura que es el dato que buscamos reflejar en los resultados.

### Para Qs despejamos:<br><br>
<img src="https://i.ibb.co/DgX4cbL/7f5b9564b088d5656c018dee6ac961d3.png" alt="7f5b9564b088d5656c018dee6ac961d3" border="0"><br>
  
Considerando que el calor es potencia por tiempo:<br><br>
<img src="http://latex2png.com/pngs/c0fb2692b7ad5b3f3266a59af89116a2.png" border="0"><br>

<img src="http://latex2png.com/pngs/f4a2167af55531928ba5d04ebcb6730f.png" border="0"><br>

``` Javascript
  window.setInterval(function () {

    mydata.push({x: time, y: temp.toFixed(4)});

    if(flag != 0){
      if(temp < tsat) {

        document.getElementById("data-table").innerHTML += `
        <div class="col-4 border bg-white text-center">`+ time +`</div>
        <div class="col-4 border bg-white text-center">`+ temp.toFixed(4) +`</div>
        <div class="col-4 border bg-white text-center">`+ mi +`</div>
        `
        time+=intervalo;
        temp = (potencia * (intervalo))/(mi*4186) + ti;
        ti = temp;

        chart.updateSeries([{
          data: mydata
        }]);
      }
 ```
 
<br>
Consideramos que para calor latente (Ql) la siguiente fórmula es útil, puesto que nos permite conocer el cambio de masa en la fase de ebullición.<br><br>

### Para Ql despejamos:<br><br>
<img src="http://latex2png.com/pngs/c4ca1a7d6b93f3b95374de946834dd81.png" border="0"><br><br>
<img src="http://latex2png.com/pngs/b2e1972a78258878066118821d031a3a.png" border="0"><br><br>


 ``` Javascript
      else {
        temp = tsat;
        mf = -((potencia * (intervalo))/(h_fg*1000)) + mi;
        mi = mf;
        console.log(temp);
        if(mf < 0){ mf = 0;}

        document.getElementById("data-table").innerHTML += `
        <div class="col-4 border bg-white text-center">`+ time +`</div>
        <div class="col-4 border bg-white text-center">`+ temp.toFixed(4) +`</div>
        <div class="col-4 border bg-white text-center">`+ mf.toFixed(4) +`</div>
        `
        time+=intervalo;
        if(mf <= 0){
          alert("proceso terminado");
          flag = 0;
        }
        else {
          chart.updateSeries([{
            data: mydata
          }]);
        }
      }
    }


  }, 1000)

}

```
