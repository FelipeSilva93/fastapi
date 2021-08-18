# Parâmetros do caminho da Url

Você pode declarar os "parâmetros" ou "variáveis" com a mesma sintaxe utilizada pelo formato de strings do Python:

```Python hl_lines="6-7"
{!../../../docs_src/path_params/tutorial001.py!}
```

O valor do parâmetro que foi passado ao path `item_id` será passado para a sua função como o argumento `item_id`.

Então, se você rodar este exemplo e for até <a href="http://127.0.0.1:8000/items/foo" class="external-link" target="_blank">http://127.0.0.1:8000/items/foo</a>, você verá a seguinte resposta:

```JSON
{"item_id":"foo"}
```

## Parâmetros de caminho com tipos

Você pode declarar o tipo de um parâmetro na função usando as anotações padrões do Python:

```Python hl_lines="7"
{!../../../docs_src/path_params/tutorial002.py!}
```

Nesse caso, `item_id` está sendo declarado como um `int`.

!!! Check
	Isso vai dar à você suporte do seu editor dentro das funções, com verificações de erros, autocompletar, etc.
	
## Conversão de <abbr title="também conhecido como: serialização, parsing, marshalling">dados</abbr>

Se você rodar esse exemplo e abrir o seu navegador em <a href="http://127.0.0.1:8000/items/3" class="external-link" target="_blank">http://127.0.0.1:8000/items/3</a>, você verá a seguinte resposta:

```JSON
{"item_id":3}
```

!!! check
	Observe que o valor recebido pela função (e também retornado por ela) é `3`, como um Python `int`, não como uma string `"3"`.
	
	Então, com essa declaração de tipo, o **FastAPI** dá pra você um <abbr title="convertendo a string que veio do request HTTP em um dado Python">"parsing"</abbr> automático no request .

## Validação de dados

Mas se você abrir o seu navegador em <a href="http://127.0.0.1:8000/items/foo" class="external-link" target="_blank">http://127.0.0.1:8000/items/foo</a>, você verá um belo erro HTTP:

```JSON
{
    "detail": [
        {
            "loc": [
                "path",
                "item_id"
            ],
            "msg": "value is not a valid integer",
            "type": "type_error.integer"
        }
    ]
}
```

devido ao parâmetro de caminho `item_id` ter um valor `"foo"`, que não é um `int`.

O mesmo erro apareceria se você tivesse fornecido um `float` ao invés de um `int`, como em: <a href="http://127.0.0.1:8000/items/4.2" class="external-link" target="_blank">http://127.0.0.1:8000/items/4.2</a>

!!! check
	Então, com a mesma declaração de tipo do Python, o **FastAPI** dá pra você validação de dados.
	
	Observe que o erro também mostra claramente o ponto exato onde a validação não passou.

	Isso é incrivelmente útil enquanto se desenvolve e debuga o código que interage com a sua API.

## Documentação

Quando você abrir o seu navegador em <a href="http://127.0.0.1:8000/docs" class="external-link" target="_blank">http://127.0.0.1:8000/docs</a>, você verá de forma automática e interativa a documtação da API como:

<img src="/img/tutorial/path-params/image01.png">

!!! check
	Novamente, apenas com a mesma declaração de tipo do Python, o **FastAPI** te dá de forma automática e interativa a documentação (integrada com o Swagger UI).
	
	Veja que o parâmetro de caminho está declarado como sendo um inteiro (int).

## Beneficios baseados em padrões, documentação alternativa

Devido ao schema gerado ser o padrão do <a href="https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.2.md" class="external-link" target="_blank">OpenAPI</a>, existem muitas ferramentas compatíveis.

Por esse motivo, o próprio **FastAPI** fornece uma API alternativa para documentação (utilizando ReDoc), que você pode acessar em <a href="http://127.0.0.1:8000/redoc" class="external-link" target="_blank">http://127.0.0.1:8000/redoc</a>:

<img src="/img/tutorial/path-params/image02.png">

Da mesma forma, existem muitas ferramentas compatíveis. Incluindo ferramentas de geração de código para muitas linguagens.

## Pydantic

Toda a validação de dados é feita por baixo dos panos pelo <a href="https://pydantic-docs.helpmanual.io/" class="external-link" target="_blank">Pydantic</a>, então você tem todos os benefícios disso. E assim você sabe que está em boas mãos.

Você pode usar as mesmas declarações de tipo com `str`, `float`, `bool` e muitos outros tipos complexos de dados.

Vamos explorar muitos destes tipos nos próximos capítulos do tutorial.

## A ordem importa

Quando você cria operações de caminho, você pode se deparar com situações onde você pode ter um caminho fixo.

Algo como `/users/me` por exemplo, digamos que esse caminho seja utilizado para pegar dados sobre o usuário atual.

E então você pode ter também um caminho `/users/{user_id}` para pegar dados sobre um usuário específico associado a um ID de usuário.

Porque as operações de caminho são avaliadas em ordem, você precisa ter certeza que o caminho para `/users/me` está sendo declarado antes do caminho `/users/{user_id}`:

```Python hl_lines="6  11"
{!../../../docs_src/path_params/tutorial003.py!}
```

Caso contrário, o caminho para `/users/{user_id}` coincidiria também para `/users/me`, "pensando" que estaria recebendo o parâmetro `user_id` com o valor de `"me"`.

## Valores predefinidos

Se você tem uma operação de caminho que recebe um parâmetro de caminho, mas que você queira que esses valores possíveis do parâmetro de caminho sejam predefinidos, você pode usar <abbr title="Enumeration">`Enum`</abbr> padrão do Python.

### Criando uma classe `Enum`

Importe `Enum` e crie uma sub-classe que herde de `str` e de `Enum`.

Por herdar de `str` a documentação da API vai ser capaz de saber que os valores devem ser do tipo `string` e assim ser capaz de mostrar eles corretamente.

Assim, crie atributos de classe com valores fixos, que serão os valores válidos disponíveis.

```Python hl_lines="1  6-9"
{!../../../docs_src/path_params/tutorial005.py!}
```

!!! informação
    <a href="https://docs.python.org/3/library/enum.html" class="external-link" target="_blank">Enumerations (ou enums) estão disponíveis no Python</a> desde a versão 3.4.

!!! dica
	Se você está se perguntando, "AlexNet", "ResNet", e "LeNet" são apenas nomes de <abbr title="técnicamente, modelos de arquitetura de Deep Learning">modelos</abbr> de Machine Learning (aprendizado de máquina).

### Declare um *parâmetro de caminho*

Logo, crie um *parâmetro de caminho* com anotações de tipo usando a classe enum que você criou (`ModelName`):

```Python hl_lines="16"
{!../../../docs_src/path_params/tutorial005.py!}
```

### Revise a documentação

Visto que os valores disponíveis para o parâmetro do caminho estão predefinidos, a documentação interativa pode mostrar esses valores de uma forma bem legal:

<img src="/img/tutorial/path-params/image03.png">

### Trabalhando com os *enumeration* do Python

O valor do *parâmetro do caminho* será um *membro de enumeration*.

#### Compare *membros de enumeration*

Você pode comparar eles com o *membro de enumeration* no enum `ModelName` que você criou:

```Python hl_lines="17"
{!../../../docs_src/path_params/tutorial005.py!}
```

#### Obtenha o *valor de enumerate*

Você pode ter o valor exato de enumerate (um `str` nesse caso) usando `model_name.value`, ou em geral, `your_enum_member.value`:

```Python hl_lines="20"
{!../../../docs_src/path_params/tutorial005.py!}
```

!!! conselho
	Você também poderia acessar o valor `"lenet"` com `ModelName.lenet.value`

#### Retorne *membros de enumeration*

Você pode retornar *membros de enum* do seu *caminho de operação*, em um corpo JSON aninhado (por exemplo um `dict`).

Eles serão convertidos para o seus valores correspondentes (strings nesse caso) antes de serem retornados ao cliente:

```Python hl_lines="18  21  23"
{!../../../docs_src/path_params/tutorial005.py!}
```

No seu cliente você vai obter uma resposta JSON como:

```JSON
{
  "model_name": "alexnet",
  "message": "Deep Learning FTW!"
}
```

## Parâmetros de caminho que contém caminhos

Digamos que você tenha uma *operação de caminho* com um caminho `/files/{file_path}`.

Mas você precisa que o próprio `file_path` contenha um *path*, como `home/johndoe/myfile.txt`.

Então, a URL para este arquivo deveria ser algo como: `/files/home/johndoe/myfile.txt`.

### Suporte do OpenAPI

O OpenAPI não suporta uma maneira de declarar um *parâmetro de path* que contenha um *path* dentro, dado que isso poderia levar a cenários que são difíceis de testar e definir.

No entanto, você pode fazer isso no **FastAPI**, usando uma das ferramentas internas do Starlette. 

A documentação continuaria funcionando, ainda que não adicionaria nenhuma informação dizendo que o parâmetro deveria conter um path.

### Conversor de caminho

Usando uma opção direta do Starlette você pode declarar um *parâmetro de caminho* contendo um *caminho* usando uma URL como:

```
/files/{file_path:path}
```

Nesse caso, o nome do parâmetro é `file_path`, e a última parte, `:path`, diz que o parâmetro deveria coincidir com qualquer *caminho*.

Então, você poderia usar ele com:

```Python hl_lines="6"
{!../../../docs_src/path_params/tutorial004.py!}
```

!!! dica
	Você poderia precisar que o parâmetro contivesse `/home/johndoe/myfile.txt`, com uma barra no inicio (`/`).
	
	Neste caso, a URL deveria ser: `/files//home/johndoe/myfile.txt`, com barra dupla (`//`) entre `files` e `home`.


## Recapitulando

Com o **FastAPI**, usando as declarações de tipo do Python, você obtém:

* Suporte no editor: verificação de erros, e opção de autocompletar, etc.
* Parsing de dados
* "<abbr title="convertendo uma string que vem de um request HTTP em dado Python">Parsing</abbr>" de dados
* Validação de dados
* Anotação da API e documentação automática

Você apenas tem que declará-los uma vez.

Essa é provavelmente a vantagem mais visível do **FastAPI** se comparado com frameworks alternativos (além do desempenho puro).