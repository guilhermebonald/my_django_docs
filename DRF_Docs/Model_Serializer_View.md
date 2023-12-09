
# Autenticação com Django Rest Framework

Essa breve documentação, tem como objetivo, simplificar o entendimento sobre o sistema de autenticação do Django Rest Framework.


## Model

Em Django, um Model é a única e definitiva fonte de informações sobre seus dados. Ele contém os campos e comportamentos essenciais dos dados que você está armazenando. Geralmente, cada modelo mapeia para uma única tabela de banco de dados. Cada campo do modelo representa um campo específico na tabela do banco de dados. 😊





```python
from django.contrib.auth.models import User
from django.db import models


class CustomUser(User):
    # campos adicionais podem ser adicionados aqui
    bio = models.TextField(max_length=500, blank=True)
    location = models.CharField(max_length=30, blank=True)
    birth_date = models.DateField(null=True, blank=True)

```
#
- No Exemplo acima herdamos a classe **User** para criarmos um usuário completo. **User** já contém campos úteis que são comumente usados na criação de usuários, como **`username`**, **`password`**, **`email`**, **`first_name`**, **`last_name`**, e também podemos criar campos adicionais como no exemplo. Além disso, lida com a segurança sobre informações sensiveis, como por exemplo, já armazenar as senhas em hashes. Para mais informações sobre a classe, é importante analisar a mesma no proprio código do Django.
#
- Model para outros tipos de dados:
```python
class Car(models.Model):
    marca = models.CharField(max_length=255)
    modelo = models.CharField(max_length=255)
    ano = models.IntegerField()
    tipo = models.CharField(max_length=255)
    cor = models.CharField(max_length=255)
    quilometragem = models.IntegerField()
```

## Serializers
Os Serializers são utilizados para controlar a saída das respostas, permitem que dados complexos, como querysets e instâncias de modelos, sejam convertidos em tipos de dados Python nativos que podem ser facilmente renderizados em **JSON**, **XML** ou **outros** tipos de conteúdo.


## Classe Serializer
```python
from rest_framework import serializers
from django.contrib.auth.models import User

class CustomUserSerializer(serializers.Serializer):
    username = serializers.CharField(max_length=150)
    email = serializers.EmailField()
    bio = serializers.CharField(max_length=500, allow_blank=True)
    location = serializers.CharField(max_length=30, allow_blank=True)
    birth_date = serializers.DateField(allow_null=True, required=False)

class CarSerializer(serializers.Serializer):
    marca = serializers.CharField(max_length=255)
    modelo = serializers.CharField(max_length=255)
    ano = serializers.IntegerField()
    tipo = serializers.CharField(max_length=255)
    cor = serializers.CharField(max_length=255)
    quilometragem = serializers.IntegerField()

```

- Usando a classe **Serializer**, o DRF só ira incluir os campos que são explicitamente declarados na classe Serializer. Se o seu modelo tiver mais campos e você não os incluir no Serializer, esses campos não serão incluídos na representação serializada. Nesse momento é que nos é apresentado o **ModelSerializer**.

## Classe ModelSerializer
Com o **ModelSerializer** a coisa se torna mais simples e prática. Primeiro, gerando automaticamente um conjunto de campos para você, já com base no **Model**. Segundo, incluindo validações simples geradas já a partir do modelo. Terceiro, incluindo por padrão os métodos **create()** e **update()**.

```python
from rest_framework import serializers
from .models import CustomUser


class UserSerializer(serializers.ModelSerializer):
    class Meta(object):
        model = CustomUser
        fields = "__all__"
        # ou fields = ['username', email...]
```

## View
As Views no Django Rest Framework (DRF) são classes que definem o comportamento de uma API. Elas processam as solicitações HTTP, executam a lógica do negócio e retornam as respostas HTTP. A classe APIView do DRF é uma extensão da classe View do Django, fornecendo funcionalidades adicionais como autenticação, controle de permissões e negociação de conteúdo. As Views são um componente central na criação de APIs com o DRF.

```python
from rest_framework.response import Response
from rest_framework.views import APIView
from main_app.serializers import UserSerializer
from .models import CustomUser


class RegisterView(APIView):
    def get(self, request):
        user = CustomUser.objects.all()
        serializer = UserSerializer(user, many=True)
        return Response(serializer.data)

    def post(self, request):
        serializer = UserSerializer(data=request.data)
        if serializer.is_valid(raise_exception=True):
            serializer.save()
            return Response(serializer.data, status=201)
        return Response(serializer.errors, status=400)

```

## Explicando o método `GET e POST`

**GET**: Quando uma solicitação GET é recebida, o método **`get`** é chamado. Ele recupera todos os objetos do Model **`CustomUser`** do banco de dados usando **`CustomUser.objects.all()`**. Em seguida, ele usa o **`UserSerializer`** para converter os objetos do modelo em um formato que pode ser convertido em JSON. O parâmetro **`many=True`** indica que vários objetos estão sendo serializados. Finalmente, ele retorna uma resposta HTTP com os dados serializados.

**POST**: Quando uma solicitação POST é recebida, o método **`post`** é chamado. Ele usa o **`UserSerializer`** para validar os dados recebidos na solicitação. Se os dados são válidos, ele salva um novo objeto Model **`CustomUser`** no banco de dados, isso acontece no **`serializer.save()`** e retorna uma resposta HTTP com os dados serializados e um código de status 201, indicando que um novo recurso foi criado com sucesso. Se os dados não são válidos, ele retorna uma resposta HTTP com os erros de validação e um código de status 400, indicando que a solicitação é inválida.