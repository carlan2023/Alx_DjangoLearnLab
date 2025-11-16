from bookshelf.models import Book                                                  
>>> book = Book.objects.create(title ="GOT", author = "George RR Martin", publication_year=1949)
>>> book