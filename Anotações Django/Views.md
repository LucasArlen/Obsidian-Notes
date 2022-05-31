>*Uma view em Django nada mais é do que uma função Python que recebe uma requisição web e devolve uma resposta. Toda a lógica para devolver a resposta desejada estará na View.*

# [Views](https://pythonacademy.com.br/blog/desenvolvimento-web-com-python-e-django-view#fun%C3%A7%C3%B5es-vs-class-based-views)
## Detalhes
A camada **View** tem a responsabilidade de processar as **[[requisições]]** vindas dos usuários, formar uma **resposta** e enviá-la de volta ao usuário. É aqui que residem nossas **lógicas de negócio**!

Ou seja, essa camada deve: **recepcionar**, **processar** e **responder**!
Para isso, começamos pelo **roteamento de [[URLs]]**!

## [[Funções]] vs *Class Based Views*

Com as [[URLs]] corretamente configuradas, o Django irá rotear a sua requisição para onde você definiu. No caso acima, sua requisição irá cair na função `views.funcionarios_por_ano()`.

Podemos tratar as requisições de duas formas: através de funções ou através de _Class Based Views_ (**CBVs** - ou apenas _Views_).

Utilizando **funções**, você basicamente vai definir uma função que recebe como parâmetro uma [[requisição]] (`request`), realizar algum processamento e retornar alguma informação.

Já as _Views_ são classes que herdam de `django.views.generic.base.View` e que agrupam diversas funcionalidades e facilitam a vida do desenvolvedor.

## Utilizando *Class Based Views*

```python

def lista_funcionarios(request):
	# Buscando funcionarios na BD
	funcionarios = Funcionario.objetos.all()

	# Incluimos no contexto
	context = {
		'funcionarios': funcionarios
	}
	
	return render(request, "templates/funcionarios.html", context)
```

Algumas colocações:

- Toda função que vai processar requisições no Django recebe como primeiro parâmetro, um objeto `request` contendo os dados dessa requisição.
- [[Contexto]] é o conjunto de dados que estarão disponíveis no _template_.
- A função `django.shortcuts.render()` é um atalho (_shortcut_) do próprio Django que facilita a renderização de _templates_: ela recebe a própria requisição, o diretório do _template_ e o contexto.

Podemos utilizar a _View_  `django.views.generic.ListView` para listar os funcionários, da seguinte forma:
``` python
from django.views.generic import ListView

class ListaFuncionarios(ListView):
	template_name = "templates/funcionarios.html"
	model = Funcionario
	context_object_name = "funcionarios"
```

É **exatamente isso** que as `Views` do Django proporcionam: elas descrevem o comportamento padrão para as funcionalidades mais simples (listagem, exclusão, busca simples, atualização).

Com isso, um objeto `funcionarios` estará disponível, **magicamente**, no seu _template_ para iteração.

Dessa forma, podemos então criar uma tabela no nosso _template_ com os dados de todos os funcionários:
``` django
<table>
	<tbody>
	
	  {% for funcionario in funcionarios %}	
		<tr>
	        <td>{{ funcionario.nome }}</td>
	        <td>{{ funcionario.sobrenome }}</td>
	        <td>{{ funcionario.remuneracao }}</td>
	        <td>{{ funcionario.tempo_de_servico }}</td>
	    </tr>
	  {% endfor %}
	  
	</tbody>
</table>

```

O Django tem uma diversidade enorme de _Views_, uma para cada finalidade, por exemplo:

- [[CreateView]]: Facilita a criação de objetos (_É o **C**reate do **C**RUD_)
- [[DetailView]]: Traz os detalhes de um objeto (_É o **R**etrieve do C**R**UD_)
- [[UpdateView]]: Facilita a atualização de um objeto (_É o **U**pdate do CR**U**D_)
- [[DeleteView]]: Facilita a deleção de objetos (_É o **D**elete do CRU**D**_)

# Funções (_Function Based Views_)

Utilizar funções é a maneira mais explícita para tratar requisições no Django (veremos que as _Class Based Views_ podem ser um pouco mais complexas pois muita coisa acontece implicitamente).

Utilizando funções, geralmente tratamos primeiro o método HTTP da requisição: foi um [[GET]]? Foi um [[POST]]? Um [[OPTION]]?

Processão a requisição da maneira desejada:
``` python

def cria_funcionario(request, pk):
	# Verificamos se o metodo POST
	if request.method == 'POST':
		form = FormularioDeCriacao(request.POST)

		if form.is_valid():
			form.save()
			return HttpResposeRedirect(reverse('list_view'))
	# Qualquer outro metodo: GET, OPTION, DELETE, etc...
	else:
		return render(request, 'templates/form.html',
		                       {'form': form})

```
