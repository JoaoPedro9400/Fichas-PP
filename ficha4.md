#Exercicio 1

~~~~
var sondagens =  function(x){
  var perc = {"ps":36.34,"psd":27.76,"cdu":6.33,"cds":4.22, "be":9.52,"pan":3.32,"chega":1.29,"il":1.29, "livre":1.09, "indecisos":8.84}
  
  //Queremos algo como = [[ps,36.34],[psd,6.33],...]
  var pares = _.toPairs(perc)
  
  //Agora vamos querer ter: [ [ps,psd,...] , [36.34,27.76,...] ]
  var Partidos_Valores = _.unzip(pares)
  
  var Partidos = Partidos_Valores[0]
  var Valores = Partidos_Valores[1]
  
  //Quero passar todos os valores para probabilidades
  var prob = map(function(p) {p/100},Valores)
  
  var numerosVotos = multinomial({ps: prob, n: x})
  
  //vamos juntar os partidos com o valores correspondentes: [[ps,x], [psd,y], ...]
  var partidos_values = _.zip(Partidos,numerosVotos)
  
  //Agora iremos criar um genero de dicionario: {}
  var dic = _.fromPairs(partidos_values)
  
  return dic
}

var poss = 3000
var Sondagem = sondagens(poss)
~~~~

#Exercicio 2 e 3

~~~~
var sondagens =  function(x){
  var perc = {"ps":36.34,"psd":27.76,"cdu":6.33,"cds":4.22, "be":9.52,"pan":3.32,"chega":1.29,"il":1.29, "livre":1.09, "indecisos":8.84}
  
  //Queremos algo como = [[ps,36.34],[psd,6.33],...]
  var pares = _.toPairs(perc)
  
  //Agora vamos querer ter: [ [ps,psd,...] , [36.34,27.76,...] ]
  var Partidos_Valores = _.unzip(pares)
  
  var Partidos = Partidos_Valores[0]
  var Valores = Partidos_Valores[1]
  
  //Quero passar todos os valores para probabilidades
  var prob = map(function(p) {p/100},Valores)
  
  var numerosVotos = multinomial({ps: prob, n: x})
  
  //vamos juntar os partidos com o valores correspondentes: [[ps,x], [psd,y], ...]
  var partidos_values = _.zip(Partidos,numerosVotos)
  
  //Agora iremos criar um genero de dicionario
  var dic = _.fromPairs(partidos_values)
  
  return dic
}

var poss = 3000
var Sondagem = sondagens(poss)



var func = function(){
  var num = 10
  
  // obter 10 numeros entre 0 e 1, segundo uma distribuicao uniforme
  var numeros = mapN(function(T) {uniform(0,1)},num)
  
  //Será util para a normalizacao
  var soma = sum(numeros)
  
  //vamos ter as probabilidades
  var prob = map(function(p) {p/soma},numeros)
  
  var dist = Multinomial({ps: prob, n: poss})
  observe(dist,_.values(Sondagem))
  
  //obter os partidos
  var Partidos = _.keys(Sondagem)
  
  //juntar as probabilidades com o partido correspondente: [[ps,x], [psd,y] , ...]
  var Partidos_Prob = _.zip(Partidos,prob)

  //Agora podemos criar um genero de dicionario: {ps:x , psd:y , ...}
  var dic = _.fromPairs(Partidos_Prob)
  
  return dic
}

var result = Infer({method:'MCMC',samples: 10000,model:function(){ return func()}})

viz.marginals(result)
~~~~

#Exercicio 4

~~~~
///////////// Exercicios anteriores ///////////////////


var sondagens =  function(x){
  var perc = {"ps":36.34,"psd":27.76,"cdu":6.33,"cds":4.22, "be":9.52,"pan":3.32,"chega":1.29,"il":1.29, "livre":1.09, "indecisos":8.84}
  
  //Queremos algo como = [[ps,36.34],[psd,6.33],...]
  var pares = _.toPairs(perc)
  
  //Agora vamos querer ter: [ [ps,psd,...] , [36.34,27.76,...] ]
  var Partidos_Valores = _.unzip(pares)
  
  var Partidos = Partidos_Valores[0]
  var Valores = Partidos_Valores[1]
  
  //Quero passar todos os valores para probabilidades
  var prob = map(function(p) {p/100},Valores)
  
  var numerosVotos = multinomial({ps: prob, n: x})
  
  //vamos juntar os partidos com o valores correspondentes: [[ps,x], [psd,y], ...]
  var partidos_values = _.zip(Partidos,numerosVotos)
  
  //Agora iremos criar um genero de dicionario
  var dic = _.fromPairs(partidos_values)
  
  return dic
}

var poss = 3000
var Sondagem = sondagens(poss)



var func = function(){
  var num = 10
  
  // obter 10 numeros entre 0 e 1, segundo uma distribuicao uniforme
  var numeros = mapN(function(T) {uniform(0,1)},num)
  
  //Será util para a normalizacao
  var soma = sum(numeros)
  
  //vamos ter as probabilidades
  var prob = map(function(p) {p/soma},numeros)
  
  var dist = Multinomial({ps: prob, n: poss})
  observe(dist,_.values(Sondagem))
  
  //obter os partidos
  var Partidos = _.keys(Sondagem)
  
  //juntar as probabilidades com o partido correspondente: [[ps,x], [psd,y] , ...]
  var Partidos_Prob = _.zip(Partidos,prob)

  //Agora podemos criar um genero de dicionario: {ps:x , psd:y , ...}
  var dic = _.fromPairs(Partidos_Prob)
  
  return dic
}

var result = Infer({method:'MCMC',samples: 10000,model:function(){ return func()}})

//viz.marginals(result)
//////////////////////////////////////////////////////////


////////////////// Exercicio 4 /////////////////////////

var estimar_intervalo = function(dist, margem, low, high) {
  expectation(marginalize(dist, margem), function(p) {low < p && p < high})
}

var HDI = function(dist, margem, low, high, delta) {
  var p = estimar_intervalo(dist, margem, low, high)
  if (p <= 0.95) return [low, high]
  var A  = estimar_intervalo(dist, margem, low + delta, high)
  var B  = estimar_intervalo(dist, margem, low, high - delta)
  return A > B ? HDI(dist, margem, low + delta, high, delta) : HDI(dist, margem, low, high - delta, delta)
}

var print_intervals = function(dist, margens) {
  map(function(m) {
    print(m + ": " + HDI(dist, m, 0, 1, 0.005))
  }, margens)
  
}

print_intervals(result,_.keys(Sondagem))
~~~~