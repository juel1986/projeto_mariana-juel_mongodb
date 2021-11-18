# projeto_mariana-juel_mongodb
const collection = 'filmes';

//criar collection

db.createCollection(collection);

//inserir dados na collection

db.filmes.insertMany([
  {nome:"Duna", diretor:"Denis Villeuneve", elenco:
  ["Timothée Chalamet", "Rebecca Ferguson", "Zendaya"], ano:"2021", premios: 0},

  {nome:"Central do Brasil", diretor:"Walter Salles", elenco:
  ["Fernanda Montenegro", "Vinicius de Oliveira"], ano:"1998", premios: 20},

  {nome:"Cidade dos Sonhos", diretor:"David Lynch", elenco:
  ["Naomi Watts", "Laura Elena Harring"], ano:"2001", premios: 18},

  {nome:"King Kong", diretor:"Peter Jackson", elenco:
  ["Naomi Watts", "Jack Black", "Adrien Brody"], ano:"2005", premios: 10},

  {nome:"Bela Vingança", diretor:"Emerald Fennel", elenco:
  ["Carey Mulligan", "Bo Burnham"], ano:"2020", premios: 22},

  {nome:"SDA: A Sociedade do Anel", diretor:"Peter Jackson", elenco:
  ["Elijah Wood", "Ian McKellen, Liv Tyler"], ano:"2001", premios: 15},

  {nome:"Shrek", diretor:"Andrew Adamson Vicky Jenson", elenco:
  ["Mike Myers", "Eddie Murphy", "Cameron Diaz"], ano:"2001", premios: 10},

  {nome:"Cidade de Deus", diretor:
  ["Fernando Meirelles", "Kátia Luind"], elenco:["Alexandre Rodrigues", "Leandro Firmino da Hora"], ano:"2002", premios: 15},

  {nome:"Pânico", diretor:"Wes Craven", elenco:
  ["Neve Campbell", "Courtney Cox", "David Arquette"], ano:"1996", premios: 6}
])


//Busque filmes lançados entre 1997 e 2004
db.filmes.find().pretty()
//Busque filmes de 2001 ou que comecem com a letra D.
db.filmes.find(
  { ano:{
      $gte:"1997", $lte:"2010" }
    }).sort({ano:1})
//Busque filmes lançados dirigidos pelo Peter Jackson ou pelo Walter Salles usando $in.
    db.filmes.find(
  { $or: [ {ano:"2001"},
      {nome: /^D/} ] } )
//Busque filmes lançados entre 2002 e 2021 e que contenham as atrizes Naomi Watts ou Carey Mulligan.
db.filmes.find(
  { diretor:{ $in:["Peter Jackson", "Walter Salles"]
      } })
//Busque todos os filmes que não sejam entre 2001 e 2005.
db.filmes.find({
    $and: [ { $or: [{ano:{$gte: "2002"}}, {ano:{$lte: "2021"}}] },
        { $or: [{elenco:{$eq: "Naomi Watts"}}, {elenco:{$eq: "Carey Mulligan"}}]}
         ] })
//Conte quantos filmes foram lançados em 2001.
db.filmes.find({
  ano:{ $not:{ $gte:"2001", 
     $lte:"2005" } } }).sort({ ano: 1 })
//Selecione apenas o elenco numa distinct.
db.filmes.distinct("elenco"),
//Selecione os filmes lançados em 2001, apresentando seu nome e diretor apenas, em ordem decrescente, limitando a dois resultados.
db.filmes.find({},{ano:"2001",diretor:1, nome:1}).limit(2).sort({nome:-1}),
//Crie uma index usando o ano como referência exibindo o index criado
db.filmes.createIndex({ano:2},{name:"Todos os anos dos filmes"}), 
//Some os prêmios de todos os filmes
db.filmes.aggregate([
    { $group: {_id:"",premios:{$sum:"$premios"}
        }}, { $project: {"total de Premios":"$premios"}} ])
//Na coleção `sample_mflix` do Mongo Atlas DB, crie uma consulta que retorne os filmes com os seus comentários utilizando o estágio de agrupamento (e.g.: `$lookup`).
use("sample_mflix")
db.comments.aggregate([
  {$lookup: { from:"movies",
    localField:"movie_id",
    foreignField:"_id",as:"Filmes"} } ])
