# Day 13

## Relationships in Spring Data REST

- One-to-One Relationship
- 2.1. The Data Model
Let's define two entity classes, Library and Address, having a one-to-one relationship by using the @OneToOne annotation. The association is owned by the Library end of the association:

@Entity
public class Library {

    @Id
    @GeneratedValue
    private long id;

    @Column
    private String name;

    @OneToOne
    @JoinColumn(name = "address_id")
    @RestResource(path = "libraryAddress", rel="address")
    private Address address;
    
    // standard constructor, getters, setters
}
@Entity
public class Address {

    @Id
    @GeneratedValue
    private long id;

    @Column(nullable = false)
    private String location;

    @OneToOne(mappedBy = "address")
    private Library library;

    // standard constructor, getters, setters
}
The @RestResource annotation is optional, and we can use it to customize the endpoint.

We must also be careful to have different names for each association resource. Otherwise, we'll encounter a JsonMappingException with the message “Detected multiple association links with same relation type! Disambiguate association.”

The association name defaults to the property name, and we can customize it using the rel attribute of the @RestResource annotation:

@OneToOne
@JoinColumn(name = "secondary_address_id")
@RestResource(path = "libraryAddress", rel="address")
private Address secondaryAddress;
If we were to add the secondaryAddress property above to the Library class, we'd have two resources named address, thus encountering a conflict.

We can resolve this by specifying a different value for the rel attribute, or by omitting the RestResource annotation so that the resource name defaults to secondaryAddress.

- 2.2. The Repositories
In order to expose these entities as resources, we'll create two repository interfaces for each of them by extending the CrudRepository interface:

public interface LibraryRepository extends CrudRepository<Library, Long> {}
public interface AddressRepository extends CrudRepository<Address, Long> {}

-2.3. Creating the Resources
First, we'll add a Library instance to work with:

curl -i -X POST -H "Content-Type:application/json" 
  -d '{"name":"My Library"}' http://localhost:8080/libraries
Then the API returns the JSON object:

{
  "name" : "My Library",
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/libraries/1"
    },
    "library" : {
      "href" : "http://localhost:8080/libraries/1"
    },
    "address" : {
      "href" : "http://localhost:8080/libraries/1/libraryAddress"
    }
  }
}
Note that if we're using curl on Windows, we have to escape the double-quote character inside the String that represents the JSON body:

-d "{\"name\":\"My Library\"}"
We can see in the response body that an association resource has been exposed at the libraries/{libraryId}/address endpoint.

Before we create an association, sending a GET request to this endpoint will return an empty object.

However, if we want to add an association, we must first create an Address instance:

curl -i -X POST -H "Content-Type:application/json" 
  -d '{"location":"Main Street nr 5"}' http://localhost:8080/addresses
The result of the POST request is a JSON object containing the Address record:

{
  "location" : "Main Street nr 5",
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/addresses/1"
    },
    "address" : {
      "href" : "http://localhost:8080/addresses/1"
    },
    "library" : {
      "href" : "http://localhost:8080/addresses/1/library"
    }
  }
}
- 2.4. Creating the Associations
After persisting both instances, we can establish the relationship by using one of the association resources.

This is done using the HTTP method PUT, which supports a media type of text/uri-list, and a body containing the URI of the resource to bind to the association.

Since the Library entity is the owner of the association, we'll add an address to a library:

curl -i -X PUT -d "http://localhost:8080/addresses/1" 
  -H "Content-Type:text/uri-list" http://localhost:8080/libraries/1/libraryAddress
If successful, it'll return status 204. To verify this, we can check the library association resource of the address:

curl -i -X GET http://localhost:8080/addresses/1/library
It should return the Library JSON object with the name “My Library.”

To remove an association, we can call the endpoint with the DELETE method, making sure to use the association resource of the owner of the relationship:

curl -i -X DELETE http://localhost:8080/libraries/1/libraryAddress
3. One-to-Many Relationship
We define a one-to-many relationship using the @OneToMany and @ManyToOne annotations. We can also add the optional @RestResource annotation to customize the association resource.

- 3.1. The Data Model
To exemplify a one-to-many relationship, we'll add a new Book entity, which represents the “many” end of a relationship with the Library entity:

@Entity
public class Book {

    @Id
    @GeneratedValue
    private long id;
    
    @Column(nullable=false)
    private String title;
    
    @ManyToOne
    @JoinColumn(name="library_id")
    private Library library;
    
    // standard constructor, getter, setter
}
Then we'll add the relationship to the Library class as well:

public class Library {
 
    //...
 
    @OneToMany(mappedBy = "library")
    private List<Book> books;
 
    //...
 
}
3.2. The Repository
We also need to create a BookRepository:

public interface BookRepository extends CrudRepository<Book, Long> { }
3.3. The Association Resources
In order to add a book to a library, we need to create a Book instance first by using the /books collection resource:

curl -i -X POST -d "{\"title\":\"Book1\"}" 
  -H "Content-Type:application/json" http://localhost:8080/books
And here's the response from the POST request:

{
  "title" : "Book1",
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/books/1"
    },
    "book" : {
      "href" : "http://localhost:8080/books/1"
    },
    "bookLibrary" : {
      "href" : "http://localhost:8080/books/1/library"
    }
  }
}
In the response body, we can see that the association endpoint, /books/{bookId}/library, has been created.

Now let's associate the book with the library we created in the previous section by sending a PUT request to the association resource that contains the URI of the library resource:

curl -i -X PUT -H "Content-Type:text/uri-list" 
-d "http://localhost:8080/libraries/1" http://localhost:8080/books/1/library
We can verify the books in the library by using the GET method on the library's /books association resource:

curl -i -X GET http://localhost:8080/libraries/1/books
The returned JSON object will contain a books array:

{
  "_embedded" : {
    "books" : [ {
      "title" : "Book1",
      "_links" : {
        "self" : {
          "href" : "http://localhost:8080/books/1"
        },
        "book" : {
          "href" : "http://localhost:8080/books/1"
        },
        "bookLibrary" : {
          "href" : "http://localhost:8080/books/1/library"
        }
      }
    } ]
  },
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/libraries/1/books"
    }
  }
}
To remove an association, we can use the DELETE method on the association resource:

curl -i -X DELETE http://localhost:8080/books/1/library
