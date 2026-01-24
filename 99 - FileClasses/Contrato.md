---
fields:
  - name: Regiões
    type: Select
    options:
      sourceType: ValuesFromDVQuery
      valuesList: {}
      valuesFromDVQuery: dv.pages('#Região').map(p => p.file.name)
    path: ""
    id: JjPLiv
  - name: Cidades/Locais
    type: Select
    options:
      sourceType: ValuesFromDVQuery
      valuesList: {}
      valuesFromDVQuery: dv.pages('#Cidade').map(p => p.file.name)
    path: ""
    id: OxAQ5w
version: "2.52"
limit: 20
mapWithTag: false
icon: package
tagNames:
filesPaths:
bookmarksGroups:
excludes:
extends:
savedViews: []
favoriteView:
fieldsOrder:
  - OxAQ5w
  - JjPLiv
---
