Exercicio 1

~~~~
var dado = function(numFaces){
  return function(){return randomInteger(numFaces)+1}
}

var lança = function(numDados,numFaces){
  return function(){return sum(repeat(numDados,dado(numFaces)))}
}

//xdy => x=num dados rolados , y=num de faces de cada dado

viz(repeat(10000,lança(5,2)), {xLabel: "Soma das faces"})
viz(repeat(10000,lança(2,5)), {xLabel: "Soma das faces"})
viz(repeat(10000,lança(4,6)), {xLabel: "Soma das faces"})
viz(repeat(10000,lança(2,100)), {xLabel: "Soma das faces"})
viz(repeat(10000,lança(100,2)), {xLabel: "Soma das faces"})
~~~~

~~~~
//OU


//devolve um array com o numero de vezes que calhou cada numero ao lançar x dados(numDados)
//var UnicoLancamento = multinomial({ps: prob , n:numDados})
//Se o resultado for: [0,1,0,1,2,0] 
/*
1-calhou 0 vezes
2-calhou 1 vez
3-calhou 0
4-calhou 1
5-calhou 2 vezes
6- nenhuma vez

Isto significa que lançamos 4 dados, ou seja, numDados=4
*/


var aux = function(x){
  return x+1
}

var multiplica = function(x1,x2){
  return x1*x2
}

var func = function(numFaces,NumDados){
  
  //da-nos um array com as probabilidades de cada face(sao todas iguais)
  var prob = repeat(numFaces,function(){return 1/numFaces})
  
  //devolve um array com o numero de vezes que calhou cada numero ao lançar x dados(numDados)
  //Exemplo: [0,1,0,0,0,1] => calhou 1 vez o 2 e o 6
  var Lancamentos = multinomial({ps: prob , n:NumDados})
  
  //Criar um array com todas as faces do dado
  var facesDoDado=mapN(aux,numFaces)
  
  //Aqui o que vamos obter é:
  //P.ex: se Lancamentos=[2,1,0,0,0,1] e facesDoDado=[1,2,3,4,5,6]
  //Vamos multiplicar os elementos de ambos o array com o mesmo index, logo o resultado era
  //arrayNumSaidasxFace=[2,2,0,0,0,6]
  var arrayNumSaidasxFace = map2(multiplica,Lancamentos,facesDoDado)
 
  //Somar os todos os valores do arrayNumSaidasxFace
  var Soma = sum(arrayNumSaidasxFace)
  
  return Soma
  
}


viz(repeat(10000,function(){return func(2,5)}))
viz(repeat(10000,function(){return func(5,2)}))
viz(repeat(10000,function(){return func(6,4)}))
viz(repeat(10000,function(){return func(100,2)}))
viz(repeat(10000,function(){return func(2,100)}))
~~~~

Exercicio 2

~~~~
var aux = function(x){
  return x+1
}

var multiplica = function(x1,x2){
  return x1*x2
}

var EliminaRepetidos = function(index,valor){
  if(valor>1){
    return 1
  }
  return valor
}


var func = function(numFaces,NumDados){
  
  //da-nos um array com as probabilidades de cada face(sao todas iguais)
  var prob = repeat(numFaces,function(){return 1/numFaces})
  
  //devolve um array com o numero de vezes que calhou cada numero ao lançar x dados(numDados)
  //Exemplo: [0,1,0,0,0,1] => calhou 1 vez o 2 e o 6
  var Lancamentos1 = multinomial({ps: prob , n:NumDados})
  
  //Eliminar os repetidos
  var Lancamentos = mapIndexed(EliminaRepetidos,Lancamentos1)
  
  //Criar um array com todas as faces do dado
  var facesDoDado=mapN(aux,numFaces)
  
  //Multiplica a face do dado pelo numero de saidas correspondente(neste caso 0 ou 1)
  var arrayNumSaidasxFace = map2(multiplica,Lancamentos,facesDoDado)
 
  //Somar os todos os valores do arrayNumSaidasxFace
  var Soma = sum(arrayNumSaidasxFace)
  
  return Soma
  
}


viz(repeat(10000,function(){return func(6,4)}))
~~~~

Exercicio 3

~~~~
/*
Vou lançar um dado de 10 faces - está predefinido pelo exercicio
Lanco-o X vezes
Apenas escolho os Y lancamentos mais altos
Se sair a face 10 tenho de lançar novamente e o valor final = 10 + valor2lancamento 
*/


var dado = function(){
  return randomInteger(10)+1 //da-nos um valor de 1 a 10
}

var Lancamento = function(){
  
  var face = dado()
  
  if(face==10){
    //temos de lancar de novo
    var face = 10 + Lancamento()
    return face
  }
  
  return face
}


var func = function(numMax,listaOrd){
  
  if(numMax==0){
    //Já nao queremos mais nenhum maximo
    return []
  }
  
  var max = listaOrd[listaOrd.length-1]//o maximo esta em ultimo lugar dado que a lista esta ordenada
  var listaNova = listaOrd.slice(0,-1)//eliminar o maximo da lista
  
  return (func(numMax-1,listaNova)).concat(max)
}



var f = function(Y,X){
  
  //Fazer os X lancamentos
  var ValoresLancamentos = repeat(X,Lancamento)
  
  //ordenar o array
  var ValoresLancamentosOrd = sort(ValoresLancamentos)
  
  //retorna a soma dos Y valores mais altos
  return sum(func(Y,ValoresLancamentosOrd))
 
  
}

//var X=1 //numero de lancamentos
//var Y=1 //numero de lancamentos altos que queremos ficar
//viz(repeat(1000,function(){ return f(Y,X)}))
viz(Infer(function(){ return f(1,1)}))
viz(Infer(function(){ return f(1,3)}))
viz(Infer(function(){ return f(1,5)}))
viz(Infer(function(){ return f(2,7)}))
viz(Infer(function(){ return f(4,9)}))
~~~~

Exercicio 4

~~~~
var dado = function(){
  return randomInteger(10)+1 //da-nos um valor de 1 a 10
}

var Lancamento = function(){
  
  var face = dado()
  
  return face
}


var func = function(numDados,TN){
  
  //Lançar os dados - da-nos um array com todos os lancamentos
  var lancamentos = repeat(numDados,Lancamento)
  
  //Agora temos de usar o filter para separar os 1´s e os sucessos
  var Insucessos = filter(function(y){return y==1}, lancamentos)
  var Sucessos = filter(function(x){ return x>=TN}, lancamentos)
  
  //Somar os sucessos e subtrai-los pelo numero de insucessos
  var numInsucessos = Insucessos.length
  var numSucessos = Sucessos.length
  var numSucessosFinal = numSucessos-numInsucessos
  
  return numSucessosFinal
  
}


var f = function(numDados,TN){
  return repeat(1000,function(){return func(numDados,TN)})
}
//viz(f(numDados,TN))
viz(f(3,5),{xLabel: 'Numero de sucessos'})
viz(f(8,5),{xLabel: 'Numero de sucessos'})
viz(f(3,9),{xLabel: 'Numero de sucessos'})
viz(f(3,1),{xLabel: 'Numero de sucessos'})
viz(f(10,10),{xLabel: 'Numero de sucessos'})



//Exemplo:
/*
Saiu = [1,1,7,5]

O nosso TN=5 => como temos 2 numeros acima ou igual a TN(7e 5) => tenho 2 sucessos
Como saiu o valor 1 => tenho insucessos
Dado que tenho 2 1´s tenho de subtrair 2 ao numero de sucessos => Valor final = sucessos - numero1´s
=> Valor final = 2 -2 =0

*/
~~~~

Exercicio 5


~~~~
var dado = function(){
  return randomInteger(10)+1 //da-nos um valor de 1 a 10
}

var Lancamento = function(){
  
  var face = dado()
  
  return face
}


var func = function(numDados,TN){
  
  //Lançar os dados - da-nos um array com todos os lancamentos
  var lancamentos = repeat(numDados,Lancamento)
  
  //Agora temos de usar o filter para separar os 1´s e os sucessos
  var Insucessos = filter(function(y){return y==1}, lancamentos)
  var Sucessos = filter(function(x){ return x>=TN}, lancamentos)
  
  //Somar os sucessos e subtrai-los pelo numero de insucessos
  var numInsucessos = Insucessos.length
  var numSucessos = Sucessos.length
  var numSucessosFinal = numSucessos-numInsucessos
  
  return numSucessosFinal
  
}


var f = function(){
  var TN = randomInteger(20)+1 //da-nos um numero aleatorio de 1 a 20
  var numDados=10
  return [TN,func(numDados,TN)]
}


viz.heatMap(repeat(1000,f))
~~~~

Exercicio 6

~~~~
//no x vamos ter o nº de dados
//no y vamos ter o numero de sucessos


var dado = function(){
  return randomInteger(10)+1 //da-nos um valor de 1 a 10
}

var Lancamento = function(){
  
  var face = dado()
  
  return face
}


var func = function(numDados,TN){
  
  //Lançar os dados - da-nos um array com todos os lancamentos
  var lancamentos = repeat(numDados,Lancamento)
  
  //Agora temos de usar o filter para separar os 1´s e os sucessos
  var Insucessos = filter(function(y){return y==1}, lancamentos)
  var Sucessos = filter(function(x){ return x>=TN}, lancamentos)
  
  //Somar os sucessos e subtrai-los pelo numero de insucessos
  var numInsucessos = Insucessos.length
  var numSucessos = Sucessos.length
  var numSucessosFinal = numSucessos-numInsucessos
  
  return numSucessosFinal
  
}


var f = function(){
  var numDados = randomInteger(10)+1 //da-nos um numero aleatorio de 1 a 10
  var TN=9
  return [numDados,func(numDados,TN)]
}


viz.heatMap(repeat(10000,f))
~~~~

Exercicio 7

~~~~
/*
Vou lançar um dado de 10 faces
Lanco-o X vezes
Apenas escolho os Y lancamentos mais altos
Se sair a face 10 tenho de lançar novamente e o valor final = 10 + valor2lancamento
*/


var dado = function(){
  return randomInteger(10)+1 //da-nos um valor de 1 a 10
}

var Lancamento = function(){
  
  var face = dado()
  
  if(face==10){
    //temos de lancar de novo
    var face = 10 + Lancamento()
    return face
  }
  
  return face
}


var func = function(numMax,listaOrd){
  
  if(numMax==0){
    //Já nao queremos mais nenhum maximo
    return []
  }
  
  var max = listaOrd[listaOrd.length-1]//o maximo esta em ultimo lugar dado que a lista esta ordenada
  var listaNova = listaOrd.slice(0,-1)//eliminar o maximo da lista
  
  return (func(numMax-1,listaNova)).concat(max)
}



var f = function(Y,X){
  
  //Fazer os X lancamentos
  var ValoresLancamentos = repeat(X,Lancamento)
  
  //ordenar o array
  var ValoresLancamentosOrd = sort(ValoresLancamentos)
  
  //retorna a soma dos Y valores mais altos
  return sum(func(Y,ValoresLancamentosOrd))
 
  
}


var jogo = function(J1,J2){
  //T-traits S=skills
  //numDados rolados = trait + skill
  //numDados guardados = trait
  var T1 = J1[0]
  var S1 = J1[1]
  var T2 = J2[0]
  var S2 = J2[1]
  var jogador1 = f(T1,T1+S1)
  var jogador2 = f(T2,T2+S2)
  
  return (jogador1>jogador2 ? 1 : jogador2>jogador1 ? 2 : 3 )
  
}
/*
1 situação: 
jog1 lança 6 dados(T+S) e fica com os 2 maiores(T)
jog2 lança 3 dados(T+S) e fica com apenas o 1(T)
É de prever que o jogador 1 ganhe quase sempre 
*/
//                           jogo([T1,S1],[T2,S2])
viz(Infer(function(){ return jogo([2,4],[1,2])}) , {xLabel:'Vencedor: 1=jog1 , 2=jog2 , 3=empate'})


/*
2 situação: 
jog1 lança 10 dados e fica com apenas 1
jog2 lança 10 dados e fica com apenas 1
É de prever que fique aproximadamente 50 50 
*/
viz(Infer(function(){ return jogo([1,9],[1,9])}) , {xLabel:'Vencedor: 1=jog1 , 2=jog2 , 3=empate'})


/*
3 situação: 
jog1 lança 5 dados e fica com apenas 5
jog2 lança 7 dados e fica com apenas 5
É de prever que ganhe o 2 mais vezes
*/
viz(Infer(function(){ return jogo([5,0],[5,2])}) , {xLabel:'Vencedor: 1=jog1 , 2=jog2 , 3=empate'})



/*
4 situação: 
jog1 lança 5 dados e fica com apenas 5
jog2 lança 10 dados e fica com apenas 5
É de prever que ganhe o 2 com uma maior percentagem que a situação 3
*/
viz(Infer(function(){ return jogo([5,0],[5,5])}) , {xLabel:'Vencedor: 1=jog1 , 2=jog2 , 3=empate'})
~~~~

Exercicio 8

~~~~
//Definimos um TN
//Cada oponente lança os dados
//Quem tiver mais sucessos ganha
//O vencedor pode ter que ganhar por uma certa margem

var dado = function(){
  return randomInteger(10)+1 //da-nos um valor de 1 a 10
}

var Lancamento = function(){
  
  var face = dado()
  
  return face
}


var func = function(numDados,TN){
  
  //Lançar os dados - da-nos um array com todos os lancamentos
  var lancamentos = repeat(numDados,Lancamento)
  
  //Agora temos de usar o filter para separar os 1´s e os sucessos
  var Insucessos = filter(function(y){return y==1}, lancamentos)
  var Sucessos = filter(function(x){ return x>=TN}, lancamentos)
  
  //Somar os sucessos e subtrai-los pelo numero de insucessos
  var numInsucessos = Insucessos.length
  var numSucessos = Sucessos.length
  var numSucessosFinal = numSucessos-numInsucessos
  
  return numSucessosFinal
  
}


var f = function(numDados,TN){
  var margem=1
  var jog1 = func(numDados[0],TN)
  var jog2 = func(numDados[1],TN)
  if (jog1>jog2){
    //ver se respeita a margem.
    //Se return 3 então é porque nenhum ganhou
    return jog1-jog2>=margem? 1 : 3
  }
  else{
    return jog2-jog1>=margem? 2 : 3
  }
  
}
viz(repeat(1000,function(){return f([6,3],2)}))
viz(repeat(1000,function(){return f([6,6],2)}))
viz(repeat(1000,function(){return f([6,15],2)}))
viz(repeat(1000,function(){return f([10,6],2)}))
viz(repeat(1000,function(){return f([10,6],4)}))
viz(repeat(1000,function(){return f([100,100],10)}))
~~~~