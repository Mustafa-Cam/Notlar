GraphQl kısaca rest'in gelişmiş halidir desek yeridir. Facebook(Meta) tarafından geliştirilmiştir.

 # GRAPHQL AVANTAJLARI

## 1. Sadece gerekli kolonları sorgulayabilmek. 

şimdi mobil ve web istemcisinden ayrı iki istek gelecek. mobil diyecek bana sadece username web diyecek bana hepsini yolla. yani aynı backend üzerinden  farklı client'lar tarafından erişip, farklı farklı cevaplar bekleyebiliyoruz. ve rest de ne oluyordu isteği iletiyorsun response olarak o modeldeki tüm kolonlar geliyordu ama graphql de istediğin kolonları iletebiliyorsun.

Burda webden gelen isteğe cevap gönderdiğimizi düşünün
```
 query WebAppPostsQuery{
    posts{
      id
      username
      description
      created_at
      comments{
        id
        username
      }
    }
  }
```


burda da mobilden gelen isteğe cevap döndürdüğümüzü düşünün
```
  query WebAppPostsQuery{
    posts{
      id
      username
      description
    }
  }

```


## 2. Tek API endpointi

Rest de her istek için farklı farklı endpointler lazım değil mi ? GraphQL de tek bir tanesi ile işimizi halledebiliyoruz. 

örnek olarak  bir istek içerisinde tüm kullanıcıları, tüm gönderileri ve tüm yorumları getirmek istersek aşağıdaki Query'i çalıştırmamız yeterli olacaktır.
```
query getAll{
    users{
      id
      username
    }

    posts{
      id
      description
    }

    comments{
      id
      text
    }
  }
```

## 3. Ilişkili Veriler

ilişkili veriler üzerinde bize çok kolaylık sağlıyor. Aşağıdaki Query, sistemde bulunan tüm kullanıcıları, her bir kullanıcının sahip olduğu gönderileri ve bu gönderilere ait yorumları listeler. 

```
 query{
    users{
      id
      username
      posts{
        id
        description
        comments{
          id
          text
        }
      }
    }
  }
```

yukardaki graphQL işlemini rest'de ise şu şekilde yapabilirdik

```
Kullanıcıları getirmek için:
GET /users

Her kullanıcının post'larını getirmek için:
GET /users/{userId}/posts

Her post'un altındaki yorumları getirmek için:
GET /posts/{postId}/comments
```

bizim graphQL ise 

```
Tüm kullanıcıları, her bir kullanıcının:
Kullanıcı ID'si (id)
Kullanıcı adı (username)
Kullanıcının sahip olduğu her bir gönderinin:
Gönderi ID'si (posts.id)
Gönderi açıklaması (posts.description)
Gönderiye ait her bir yorumun:
Yorum ID'si (posts.comments.id)
Yorum metni (posts.comments.text)
```

bu bilgileri bize getirecektir. 


## 4. Otomatik Dökümanlama

GraphQL şemalarınızı tasarlamaya başladığınız anda sizin için otomatik olarak dokümanlar oluşturulur. Bu dokümanlar API'ı implemente edecek olan frontend geliştiricinin ihtiyaç duyduğu tek şeydir. Bu dokümanı inceleyerek hızlıca implementasyon yapmaya başlayabilir. Artık dokümanlama için de ekstra efor sarf etmenize gerek yok.[patika.dev]

# GRAPHQL TERMİNOLOJİSİ

## Query
Get metodu olarak düşünebilirsiniz.

Bu Query, veritabanında bulunan 3 id'li ürünün id,title,description ve price kolonlarını getirecektir.
```
  query{
    products(id: 3){
      id
      title
      description
      price
    }
  }
```

## 2. Mutation
Bunu da rest'deki POST, UPDATE, PATCH, DELETE metodları olarak düşünebilirsiniz.

Yeni bir kullanıcı oluşturmak istersek aşağıdaki gibi bir mutation çalıştırabiliriz.
```
 mutation{
    insertUser(
data: {
username: "Mehmet"
surname: "Seven"
isActive: false
      }
    ){
      id
      username
      surname
      isActive
    }
  }
```
Bu sadece kullanıcı create etmek için bir sorgu güncelleme ve silme işlemlerinide yapabiliriz. 

# 3. Subscription
GraphQl gerçek zamanlı uygulamalarda efsanedir. Mesela chat uygulamalarında graphQL çok büyük kolaylıklar sağlar. 

örnek olarak Bir kullanıcı güncellendiğinde, bu kullanıcı ile alakalı güncel verilere ulaşmak istersek çalıştırmamız gereken query aşağıdaki gibi olacak;
```
subscription{
    userUpdated{
      id
      username
      surname
    }
  }
```

# Non Nullable Fields
Alanların sonuna ! işareti koyarsanız bu bu alanın null olamayacağını gösterir. Dökümanlardan bakabilirsiniz. ( https://www.apollographql.com/blog/using-nullability-in-graphql )


# Custom Types
mesela bir yazarın birden fazla kitabı olabilir ama bir kitabın bir yazarı vardır değil mi ?

```
type Author {
  id: ID!
  name: String!
  books: [Book!]!
}

type Book {
  id: ID!
  title: String!
  author: Author!
}

type Query {
  author(id: ID!): Author
  book(id: ID!): Book
  authors: [Author!]!
  books: [Book!]!
}

```
