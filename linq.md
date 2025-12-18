10 métodos do LINQ que todo programador .NET precisa saber

*1 Pegar o primeiro elemento: 
.First();
.FirstOrDefault("aqui coloca o valor padrao"); se não achar o First ele retorna um valor padrão

*2 Verificar se um elemento existe:
.Any(x => expressão que deve ser satisfeita) retorna true/false

*3 Verificar se todos os elementos são de um mesmo tipo
.All(x =>  expressão que deve ser satisfeita)

*4 Contar ( substitui o for na hora de contar coisas)
.Count( expressão que deve ser satisfeita)

*5 buscar por elemento especifico / utilizado para paginação
.ElementAt(indice)

*6 utilizado para pegar um intervalo determinado -> ele cria uma nova lista
.Take(1..6); pega a lsita de 1 até 6

*7 é O AONDE UTILIZADO PARA FILTRAR -> ele cria uma nova lista

.wHERE( expressão que deve ser satisfeita)

*8 ele é utilizado para encontrar um elemento unico, caso tenha repeticoes ele retorna o default ou da erro
.Single()
.SingleOrDefault()

*9 para pegar o ultimo elemento da lista ou de uma regra especifica por exemplo a ultima banana da lista

.Last()

* pula algo pelo indice (muito utilizado em conjunto com o take) 
Skip() 

