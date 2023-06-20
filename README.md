# PROJETO FINAL DA DISCIPLINA - BANCO DE DADOS GEOGRÁFICOS

O objetivo do Projeto foi criar uma base de dados com informações geográficas, e apresentar as informações, consultas e também o mapa com as localizações.

![imagem banco de dados](/Img/Banco_de_dados/Banco_de_dados.png)
> Base de dados feita dentro SpatiaLite


## O que foi realizado

Para elaboração deste projeto foi escolhido a dentro de uma gama de possibilidades, A opção Opção 1 e criar tabelas relacionadas a hotéis, Pontos Turísticos e Restaurantes, Ruas, Municípios:

1. [x] Tabela com municípios de um estado
2. [x] Tabela com Hotéis que pertencem a municípios
3. [x] Tabela com Restaurantes que pertencem a municípios
4. [x] Tabela com Pontos Turísticos que pertencem a municípios
5. [x] Tabelas com as ruas do município


[Arquivos Shapefiles para Municípios](/estados/SC/)

[Arquivos Shapefiles para Hotéis](/Tabelas_populadas/Hoteis/)

[Arquivos Shapefiles para Restaurantes](/Tabelas_populadas/Restaurantes/)

[Arquivos Shapefiles para Pontos Turísticos](/Tabelas_populadas/Pontos_turisticos/)

[Arquivos Shapefiles para Ruas Balneário Camboriú](/Estradas/Balneario_camboriu/)

[Arquivos Shapefiles para Ruas Camboriú](/Estradas/Camboriu/)

[Tabelas Populadas em csv](/Tabelas_populadas/csv/) (Hotéis, Restaurantes, Pontos Turísticos)
> As tabelas foram populadas com dados de Balneário Camboriú e Camboriú
### Diagrama Entidade Relacionamento OMT-G

![imagem OMT-G](/Img/Banco_de_dados/Diagrama_omt-g.png)
> O diagrama foi feito no site [aqui.io](http://aqui.io/omtg/#)

### As tabelas criadas e populadas para Balneário Camboriú e Camboriú
![imagem tabela pontos turísticos](/Img/Banco_de_dados/Tabale_pontosTuristicos.png)
> Tabela de Pontos Turísticos
---
![imagem tabela Hotéis](/Img/Banco_de_dados/Tabela_hoteis.png)
> Tabela de Hotéis
---
![imagem tabela Restaurantes](/Img/Banco_de_dados/Tabela_restaurantes.png)
> Tabela de Restaurantes
---
![imagem das ruas](/Img/Mapa_Qgiz/Ruas_balnearioCamboriu.png)
> Imagem das ruas de Balneário Camboriú
---
![imagem das ruas](/Img/Mapa_Qgiz/Ruas_camboriu.png)
> Imagem das ruas de Camboriú
--- 
### Resultado Final do Mapa
![imagem mapa](/Img/Mapa_Qgiz/MapaPopulado.png)

# Consultas SQL
As consulta foram feita no banco de dados Spacialite, sendo 5 consultas espaciais e 6 consultas não espaciais, as consultas podem ser visualizadas abaixo, ou nas pasta que contem os prints tirados das consulta dentro do QGIZ [Consulta Espacial](/Img/Consulta_espacial/) e [Consulta Não Espacial](/Img/Consulta_nao_espacial/).

## 6 - Consultas Não Espaciais

1. Consulta 01 - Mostrar tudo da tabela de Pontos turísticos que são do tipo natural

```sql
SELECT * FROM pontos_turisticos where tipo='natural'
```
![imagem consulta 1 nao espacial](/Img/Consulta_nao_espacial/Consulta-01_Vista_N_Espacial.png)

2. Mostrar nome, cidade e geom da tabela hotéis em que são de Balneário Camboriú em ordem crescente

```sql
SELECT name,cidade FROM hoteis where cidade='Balneario Camboriu' order by name asc
```
![imagem consulta 2 nao espacial](/Img/Consulta_nao_espacial/Consulta-02_Vista_N_Espacial.png)

3. Mostrar tudo da tabela de restaurantes onde cidade é igual a camboriú e com nome do contendo “restaurantes” em ordem crescente

```sql
SELECT * FROM restaurantes where cidade='Camboriu' AND name LIKE '%restaurante%' order by name asc
```
![imagem consulta 3 nao espacial](/Img/Consulta_nao_espacial/Consulta-03_Vista_N_Espacial.png)

4. Mostre nomes das tabelas restaurantes, hotéis e pontos turísticos, que são de camboriú

```sql
select h.name, p.name, r.name from restaurantes r
join hoteis h ON "Camboriu" = h.cidade
join pontos_turisticos p on "Camboriu" = p.cidade
```
> Sem vista

5. Mostre o geocode e a cidades de Camboriú e Balneário Camboriú

```sql
select geocodigo, nome from estados_sc  where estados_sc.nome = "Balneário Camboriú" or estados_sc.nome = "Camboriú"
```
> Sem vista

6. Mostre a geom em formato de texto da tabela restaurante onde nome contem mexicano.
```sql
Select st_astext(geom) from restaurantes where name like '%mexicano%'
```
> Sem vista
## 5 - Consultas Espaciais

1. Todos nomes e geom da tabela de hotéis onde estão a 10 metros de ruas residenciais de Balneário Camboriú

```sql
select h.name,h.geom from estrada_balneario e, hoteis h where st_distance(h.geom, e.geom) <= 0.0001
```
![imagem consulta 1 espacial](/Img/Consulta_espacial/Consulta-01_Vista_Espacial.png)

2. Todos os nomes de pontos turísticos que estão próximos a um hotel a uma distância de menos de 1 quilômetro

```sql
select p.name,p.geom from hoteis h,pontos_turisticos p where st_distance(p.geom, h.geom) <= 0.01
```
![imagem consulta 2 espacial](/Img/Consulta_espacial/Consulta-02_Vista_Espacial.png)

3. Todos os restaurantes que contem em Camboriú e que tem o nome de mexicano

```sql
SELECT r.* from camboriu e, restaurantes r where st_contains(e.geom, r.geom) and r.name like '%mexicano%'
```
![imagem consulta 3 espacial](/Img/Consulta_espacial/Consulta-03_Vista_Espacial.png)
4. Todos os pontos turísticos que não pertencem a cidade de camboriú e que são do tipo arquitetônico

```sql
select p.* FROM camboriu c, pontos_turisticos p where st_disjoint(p.geom, c.geom) AND p.tipo="arquitetonico"
```
![imagem consulta 4 espacial](/Img/Consulta_espacial/Consulta-04_Vista_Espacial.png)

5. Os 3 hotéis mais próximos da coordenada fornecida(restaurante com nome mexicano).

```sql
Select st_astext(geom) from restaurantes where name like '%mexicano%'
SELECT st_distance(st_geomfromtext('POINT(-48.661647 -27.026489)', 4674), geom) AS distancia, * FROM hoteis ORDER BY distancia ASC limit 3
```
![imagem consulta 5 espacial](/Img/Consulta_espacial/Consulta-05_Vista_Espacial.png)
