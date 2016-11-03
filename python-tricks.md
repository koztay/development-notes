**1 - dictionary 'den en güvenli key çekme yöntemi :**

```python
some_dict = {}
some_dict['url']  #eğer url isminde key yoksa hata verir 
some_dict.get('url', "http://www.myurl.com")  # en güvenlisi bu
```

tuple 'daki ikinci eleman default değer o varsa key 'i bulamazsa
o değeri döndürür, bulursa key 'i döndürür. Eğer default yoksa
none döndürür.

