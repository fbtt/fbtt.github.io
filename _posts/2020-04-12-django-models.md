---
layout: post
title:  "Django models"
author: battisti
categories: programming
tags:	django
cover:  "/assets/django.jpg"
---

A ideia deste post é apresentar o mapeamento dos modelos do Django para as tabelas do banco de dados. Por fins didáticos, algumas etapas não serão apresentadas, como a construção e aplicação dos arquivos de migração e o carregamento de dados para as tabelas.

## Uma breve introdução

Um modelo em Django é uma classe Python que herda a classe <code>Model</code>. Esse modelo será mapeado para uma tabela do banco de dados onde os campos do modelo (atributos) serão mapeados para colunas desta tabela.

O código abaixo mostra como criar um modelo chamado Pessoa.

{% highlight python %}
from django.db import models

class Pessoa(models.Model):
    nome = models.CharField(max_length=50)
    altura = models.IntegerField()
{% endhighlight %}

Foram utilizados dois atributos. O *nome* foi configurado para armazenar texto de até 50 caracteres e a *altura* foi especificada para armazenar números inteiros.

Sempre vamos definir atributos para criar os modelos. Cada atributo possui um [tipo][django-fields], que informa se ele armazenará um número inteiro, número com vírgula, string, data, etc.

O modelo Pessoa, que criamos acima, será mapeado para uma tabela chamada Pessoa no banco de dados.

<h3 class="tables">Pessoa</h3>

<table cellspacing="0" cellpadding="0">
  <tr>
    <th>id</th><th>nome</th><th>altura</th>
  </tr>
  <tr>
    <td>1</td><td>Pedro</td><td>182</td>
  </tr>
  <tr class="even">
    <td>2</td><td>Juca</td><td>168</td>
  </tr>
  <tr>
    <td>3</td><td>Marcos</td><td>176</td>
  </tr>
  <tr>
    <td>...</td><td>...</td><td>...</td>
  </tr>
</table>

Perceba que os atributos *nome* a *altura* foram mapeados para colunas. Além disso o Django automaticamente adicionou a coluna *id* sem precisarmos defini-lo.

Na prática, o nome da tabela ficará um pouco diferente, mas por motivos didáticos vamos considerar que ele será o mesmo da classe.

## Adicionando um relacionamento

O Django nos fornece formas de definir os três tipos de relacionamentos mais frequentes nos banco de dados relacionais: um-para-um, muitos-para-muitos e um-para-muitos.

Vamos exemplificar o relacionamento um-para-muitos criando um modelo chamado Exame:

{% highlight python %}
from django.db import models

class Exame(models.Model):
    pessoa = models.ForeignKey(Pessoa, on_delete=models.CASCADE)
    especialidade = models.CharField(max_length=30)
    data = models.DateTimeField()
    descricao = models.CharField(max_length=1500)
{% endhighlight %}

Para definir um relacionamento um-para-muitos utilizamos o tipo <code>models.ForeignKey</code>, passando como argumento a classe que queremos criar a relação.

O atributo *pessoa* utiliza o tipo <code>models.ForeignKey</code> passando a classe Pessoa. Isso nos permite relacionar um exame com uma pessoa e uma pessoa com um ou mais exames, que é o comportamento desejado. Os demais atributos do modelo são a *especialidade*, a *data* e a *descricao*.

Esse modelo criará uma tabela no banco de dados com o seguinte formato:

<h3 class="tables">Exame</h3>

<table cellspacing="0" cellpadding="0">
  <tr>
    <th>id</th><th>pessoa_id</th><th>modalidade</th><th>data</th><th>descricao</th>
  </tr>
  <tr>
    <td>1</td><td>3</td><td>eeg</td><td>2021-02-15 12:30:30</td><td>O paciente relatou que ...</td>
  </tr>
  <tr class="even">
    <td>2</td><td>1</td><td>ecg</td><td>2021-03-31 15:47:49</td><td>Bla blá</td>
  </tr>
  <tr>
    <td>3</td><td>1</td><td>ecg</td><td>2021-04-01 12:13:32</td><td>Ble blé</td>
  </tr>
  <tr>
    <td>...</td><td>...</td><td>...</td><td>...</td><td>...</td>
  </tr>
</table>

Podemos notar novamente que os atributos do modelo foram mapeados para colunas e o Django adicionou automaticamente a coluna *id*.

O atributo *pessoa*, que foi convertido para a coluna *pessoa_id*, merece um pouco de atenção. Não é ele justamente quem faz a relação com a tabela Pessoa?

## Atributo pessoa

A coluna *pessoa* da tabela Exame é responsável por "conectar" as tabelas Exame e Pessoa.

De acordo com as tabelas criadas acima, observamos que na tabela Exame, temos duas linhas (dois exames) que pertencem a mesma pessoa (*pessoa_id* com valor 1), ou seja, esse relacionamento está permitindo o comportamento que gostaríamos, onde uma pessoa pode ter vários exames e um exame pertence somente a uma pessoa.

![Relacionamento um-para-muitos](/assets/relacao.png)


## Referências

* [Django models]

[Django models]: https://docs.djangoproject.com/en/3.1/topics/db/models/
[django-fields]: https://docs.djangoproject.com/en/3.2/ref/models/fields/#field-types