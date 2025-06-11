# Criando uma API com Django

Este guia ensina como criar uma API pública com Django. O projeto será chamado `core`, com dois apps: `produtos` e `api`.

## Pré-requisitos

- Python 3.8+
- pip
- virtualenv

## 1. Criação do Projeto

```bash
python -m venv venv
source venv/bin/activate  # No Windows: venv\Scripts\activate
pip install django djangorestframework
django-admin startproject core .
```

## 2. Criação dos Apps

```bash
python manage.py startapp produtos
python manage.py startapp api
```

## 3. Configuração do `settings.py`

No `core/settings.py`, adicione:

```python
INSTALLED_APPS = [
    ...
    'rest_framework',
    'produtos',
    'api',
]
```

## 4. Model de Produto (`produtos/models.py`)

```python
from django.db import models

class Produto(models.Model):
    nome = models.CharField(max_length=100)
    descricao = models.TextField()
    preco = models.DecimalField(max_digits=10, decimal_places=2)

    def __str__(self):
        return self.nome
```

```bash
python manage.py makemigrations
python manage.py migrate
```

## 5. Serializer (`api/serializers.py`)

```python
from rest_framework import serializers
from produtos.models import Produto

class ProdutoSerializer(serializers.ModelSerializer):
    class Meta:
        model = Produto
        fields = '__all__'
```

## 6. Views (`api/views.py`)

```python
from rest_framework import viewsets
from produtos.models import Produto
from .serializers import ProdutoSerializer

class ProdutoViewSet(viewsets.ModelViewSet):
    queryset = Produto.objects.all()
    serializer_class = ProdutoSerializer
```

## 7. URLs (`api/urls.py`)

```python
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import ProdutoViewSet

router = DefaultRouter()
router.register(r'produtos', ProdutoViewSet)

urlpatterns = [
    path('', include(router.urls)),
]
```

## 8. Incluir URLs no Projeto (`core/urls.py`)

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('api.urls')),
]
```

## 9. Testar

Execute o servidor:

```bash
python manage.py runserver
```

Acesse `http://127.0.0.1:8000/api/produtos/` para ver a API com funcionalidades de listagem, cadastro, edição e remoção.

## Fim
API criada com Django e DRF pronta para uso público.