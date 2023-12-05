
# Autenticação com Django Rest Framework

Essa breve documentação, tem como objetivo, simplificar o entendimento sobre o sistema de autenticação do Django Rest Framework.


## Model

Em Django, um Model é a única e definitiva fonte de informações sobre seus dados. Ele contém os campos e comportamentos essenciais dos dados que você está armazenando. Geralmente, cada modelo mapeia para uma única tabela de banco de dados. Cada campo do modelo representa um campo específico na tabela do banco de dados. 😊




## Exemplo:

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