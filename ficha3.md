#FICHA 1

#Exercicio 1

~~~~
var function1 = function(){
  var covid = flip(0.01)
  var positivos = covid ? flip(0.7) : flip(0.1)
  return {'positivo': positivos}
}

var function2 = function(){
  var covid = flip(0.01)
  var positivo = covid ? flip(0.7) : flip(0.1)
  return {'covid': covid}
}


var function3 = function(){
  var covid = flip(0.01)
  //Se tem covid, entao tem 70% de prob de dar positivo
  //Se nao tem covid, tem 10% de prob de dar positivo
  var positivoA = covid ? flip(0.7) : flip(0.1)
  //As condições são:  A pessoa tem covid
  //                   A pessoa nao tem covid
  condition(covid==1)
  return {'Sabendo Covid, prob positivo': positivoA}
}


//probabilidade de dar positivo
//Valor teorico: 0.7*0.01 + 0.1*0.99 = 0.106
viz(Infer(function1))
viz.table(Infer(function1))

//Este é de esperar que seja = 0.01 = prob de ter covid
viz(Infer(function2))
viz.table(Infer(function2))

//Sabendo que tem covid, quero a prob de ser positivo
//valor teorico: P(positivo|covid) = 0.7
viz(Infer(function3))
viz.table(Infer(function3))
~~~~

#Exercicio 2

~~~~
var exe = function(){
  var covid = flip(0.01)
  //Se tem covid, entao tem 70% de prob de dar positivo
  //Se nao tem covid, tem 10% de prob de dar positivo
  var positivoA = covid ? flip(0.7) : flip(0.1)
  //As condições são:  A pessoa tem covid
  //                   A pessoa nao tem covid
  
  var positivoB = covid ? flip(0.9) : flip(0.01)
  
  var testeA_ou_B = flip(0.8) ? positivoA : positivoB
  //var x = flip(0.2) ? positivoB : positivoA
  return (testeA_ou_B)
}

viz(Infer(exe))

viz.table(Infer(exe))
~~~~

#Exercicio 3

~~~~
/*
Nota: É de esperar que o valor seja o seguinte
P(Covid|positivo) = [P(covid)*P(positivo|covid)]/P(positivo) = (0.01 * 0.7)/0.106 = 0.066
*/

var exeA = function(){
  var covid = flip(0.01)
  var positivo = covid ? flip(0.7) : flip(0.1)
  condition(positivo==1)
  return {'Sabendo positivo, prob de Covid' : covid}
}

viz(Infer(exeA))
viz.table(Infer(exeA))
~~~~

#Exercicio 4


Nota: Realização dos valores teóricos!

Consideremos 1000 pessoas. 1% delas têm covid , logo ficamos com 10 pessoas com covid e 990 sem covid. Das pessoas que ligam para a saude 24, 80% faz o teste A e 20% faz o teste B. Assim, dos que têm covid, 8(10*0.8) fazem o teste A e 2(10*0.2) fazem o Teste B. Por sua vez, dos que não têm covid, 792(990*0.8) fazem o teste A e 198(990*0.2) o teste B. Relativamente aos que têm covid, sabemos que fazendo o teste A, 70% dos que têm covid testam positivo, logo ficamos com 5.6(8*0.7) pessoas com positivo e 2.4(8*0.3) com negativo e tal como era de esperar 5.6+2.4=8. Relativamente ao teste B, 90% dos que têm covid tem um teste positivo, ou seja, ficamos com 1.8(2*0.9) pessoas com positivo e 0.2(2*0.1) com negativo.
Mudemos agora o 'lado' para os que não têm covid. Vimos que 792 pessoas que não têm covid fizeram o teste A. Sabemos no entanto, que segundo o teste A, 10% das pessoas que não têm covid testam positivo, ou seja, vão testar positivo 79.2(792*0.1) pessoas. E assim, 712.8(792*0.9) testam negativo. Já para quem fez o teste B, sabemos que 1% dos que não têm covid testam positivo, logo 1.98(198*0.01) testam positivo e 196.02(198*0.99) testam negativo.

#Exercicio 4.1

~~~~
//Quer P(Covid|positivo)
//P(Covid|positivo)=(5.6+1.8)/(5.6+1.8+79.2+1.98) = 0.0835
//Deste modo o True deve dar 0.0835

var exe = function(){
  var covid = flip(0.01)
  var positivoA = covid ? flip(0.7) : flip(0.1)
  var positivoB = covid ? flip(0.9) : flip(0.01)
  var positivos = flip(0.8) ? positivoA : positivoB
  
  condition(positivos==1)
  
  return (covid)
}

viz(Infer(exe))

viz.table(Infer(exe))
~~~~

#Exercicio 4.2

~~~~
//Quer P(teste A|positivo)
//P(teste A|positivo)=(5.6+79.2)/(5.6+1.8+79.2+1.98) = 0.9573

var exe = function(){
  var covid = flip(0.01)
  var positivoA = covid ? flip(0.7) : flip(0.1)
  var positivoB = covid ? flip(0.9) : flip(0.01)
  var testeA = flip(0.8)
  var positivos = testeA ? positivoA : positivoB
  
  condition(positivos==1)
  
  return (testeA)
}

viz(Infer(exe))

viz.table(Infer(exe))
~~~~

#Exercicio 4.3

~~~~
//Quer P(teste A com teste positivo e covid|positivo)
//P(covid + teste A + teste positivo|positivo) = 5.6/(5.6+1.8+79.2+1.98)=0.06322(a ultima casa decimal foi arrerdondada)

var exe = function(){
  var covid = flip(0.01)
  var positivoA = covid ? flip(0.7) : flip(0.1)
  var positivoB = covid ? flip(0.9) : flip(0.01)
  var testeA = flip(0.8)
  var positivos = testeA ? positivoA : positivoB
  
  condition(positivos==1)
  
  return (positivoA && testeA && covid)
}

viz(Infer(exe))

viz.table(Infer(exe))
~~~~