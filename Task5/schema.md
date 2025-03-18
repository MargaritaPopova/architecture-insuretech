### Запросы

```(graphql)
type Query {
  # Получить информацию о клиенте (без документов и родственников) - эквивалент /clients/{id}
  client(id: String!): Client

  # Получить только список документов клиента - эквивалент /clients/{id}/documents
  clientDocuments(id: String!): [Document!]!

  # Получить только список родственников клиента - эквивалент /clients/{id}/relatives
  clientRelatives(id: String!): [Relative!]!
}
```

### Сущности

```(graphql)
type Client {
  id: String!
  name: String!
  age: Int!
  documents: [Document!]!  # Вложенные документы
  relatives: [Relative!]!  # Вложенные родственники
}

type Document {
  id: String!
  type: String!
  number: String!
  issueDate: String!
  expiryDate: String!
}

type Relative {
  id: String!
  relationType: String!
  name: String!
  age: Int!
}
```


### Примеры запросов:
#### REST /clients/{id}

```(graphql)
query {
  client(id: "123") {
    id
    name
    age
    documents {  # используя такую вложенность, можно оптимизировать запросы к апи - вместо трех отправлять один!
      id
      type
    }
    relatives {
      name
      relationType
    }
  }
}
```

#### REST /clients/{id}/documents

```(graphql)
query {
  clientDocuments(id: "123") {
    id
    type
    number
  }
}
```

#### REST /clients/{id}/relatives

```(graphql)
query {
  clientRelatives(id: "123") {
    name
    relationType
  }
}
```

Возможности этого апи можно расширить, например, добавить связь между документом и его хозяином,
и тогда с помощью одного и того же Query можно получать еще и информацию о клиенте внутри информации о документе:

```(graphql)
type Document {
  id: String!
  type: String!
  number: String!
  issueDate: String!
  expiryDate: String!
  client: Client!  # вот тут добавить связь
}
```

```(graphql)
query {
  clientDocuments(id: "123") {
    id
    type
    number
    client {
      id
      name
      age
    }
  }
}
```
