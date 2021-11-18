Exercicio 1.1


~~~~
//Considera-se que true=cara e false=coroa

var f = function(){
  
  return flip()
}

var n = 10
var lista = repeat(n,f)
var ValorFinal=sum(lista)
console.log(lista)
console.log("Saíram",ValorFinal,"caras")
~~~~

Exercicio 1.2

~~~~
var f = function(){
  
  return flip()
}

var n=3

var lista = repeat(n,f)

console.log(lista)


if(sum(lista)==0){
  console.log("Tudo Coroa")
}
else{
  console.log("Tem caras")
}
~~~~

Exercicio 1.3

~~~~
//O objetivo é ver se da tudo True ou False

var f = function(){
  return flip()
}

var lancamentos=3

var lista = repeat(lancamentos,f)
console.log(lista)

if(sum(lista)==lancamentos){
  console.log("Tudo Cara")
}
else{
  if(sum(lista)==0){
    console.log("Tudo Coroa")
  }
  else{
    console.log("Misto")
  }
}
~~~~

Exercicio 2

~~~~
viz(repeat(1000,flip) , {xLabel: 'False=Tails | True=Head' , yLabel: 'MineFrequency'} )
//O viz é o que transforma a lista num histograma
~~~~

Exercicio 3

~~~~
var prob = function(x){
  return function(){ return flip(x)}
}


var probCara=0.3
viz(repeat(1000,prob(probCara)))
~~~~

Exercicio 4.1

~~~~
//O que entendi é que se vai lançar 5 moedas de uma vez, contar o numero de caras e 
//repetir isso 1000 vezes. Por fim, construir o histograma


var moedas5 = function(x){
  return function(){return flip(x) + flip(x) + flip(x) + flip(x) + flip(x)}
}

var probCara=0.5

var data = repeat(100000,moedas5(probCara))

viz(data , {xLabel : 'number of heads'})


/*
var moedas5 = function(x){
  return function(){return flip(x) + flip(x) + flip(x) + flip(x) + flip(x)}
}

var probCara=0.5

var data = Infer(moedas5(probCara))

viz(data , {xLabel : 'number of heads'})

*/
~~~~

Exercicio 4.2

~~~~
var moedas5=function(){
  return Binomial({p:0.5,n:5})
}


viz(moedas5())
~~~~

Exercicio 5

~~~~
var dado = function(){
  return randomInteger(6) + 1
}

var lancamentos=10000

/*
viz(repeat(lancamentos,dado))
*/

viz(Infer(dado))
~~~~

~~~~
var data = function (){
  return categorical([(1/6),(1/6),(1/6),(1/6),(1/6),(1/6)],["1","2","3","4","5","6"])
}

viz(repeat(10000,data) , {xLabel : 'Faces'})
~~~~

~~~~
var data = function (){
  return uniformDraw(["1","2","3","4","5","6"])
}

viz(repeat(10000,data) , {xLabel : 'Faces'})
~~~~

Exercicio 6

~~~~
var dado = function(){
  return randomInteger(6)+1
}

//======== Func1 modo 1 ==========
var func1 = function(numDados){
  return function(){ return sum(repeat(numDados,dado))}
}
//Da-nos a soma dos x dados num único Lançamento

//======== Func1 modo 2 ========== 
//Nao funciona assim porque o Infer tem de receber uma função sem argumentos
//var func1 = function(n){
//  return sum(repeat(n,lança))
//}


var numdados=3
var numLancamentos=10000
viz(repeat(numLancamentos,func1(numdados)), {xLabel:"Soma das faces"})

//viz(Infer(func1(numdados)))
~~~~

Exercicio 7

~~~~
//Cada baralho tem 52 cartas
//naipes: [1=C,2=H,3=D,4=S] C=clubs, H=hearts, D=diamonds, S=spades
//[A,2,3,4,5,6,7,8,9,10,J,Q,K] J=jack, Q=queen, K=king, A=ace

var data = function (){
  return [uniformDraw(['A','2','3','4','5','6','7','8','9','10','J','Q','K']) , uniformDraw(['C','H','D','S'])]
}

var mao = data()
console.log(mao[0],mao[1])
~~~~

Exercicio 8

~~~~
var lista = ['AC','AH','AS','AD','2C','2H','2S','2D','3C','3H','3S','3D','4C','4H','4S','4D','5C','5H','5S','5D','6C','6H','6S','6D','7C','7H','7S','7D','8C','8H','8S','8D','9C','9H','9S','9D','10C','10H','10S','10D','JC','JH','JS','JD','QC','QH','QS','QD','KC','KH','KS','KD']

var n = 7//escolho o numero de cartas na mao


var data = function (lista){
  return uniformDraw(lista)
}

var func = function(n,lista){
  
  if(n==0){
    console.log("Acabou")
  }
  else{
   console.log("Carta",n,":")
   var mao = data(lista)
   if(mao[0]=='1' && mao[1]=='0'){
     //É porque estamos a lidar com o 10 e aqui a printar já não posso fazer mao[0],mao[1]
     //Caso fizesse apenas ia printar 10 e não o numero+naipe
     console.log('10',mao[2])
   }
    else{
      console.log(mao[0],mao[1])
    }
   var lista2 = remove(mao,lista)//Para não haver a possibilidade de a carta se repetir
   func(n-1,lista2)
  }
  
}

func(n,lista)
~~~~