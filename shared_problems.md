_Текст не проверялся на опечатки, однако старался передать смысл. Хорошо, если его можно читать_

# Общие проблемы при написании задания
Здесь указаны проблемы, которые встречаются часто, но не у всех.

## Ошибки в том как писать Equals
На MSDN есть убер подробный гайд по тому, как это делать. Он необязателен к полному выполнению, но сейчас распишу мелкие ошибки, которые у вас часто повторялись. Все они там описаны, так что [прочтите][1].

### Нет проверки на _null_
В методах, которые вы пишите нет проверки входящего параметра на `null`, однако потом вы обращаетесь к полям этого объекта, предварительно не сделав валидацию на `null`.
Лучше писать вот так:
```C#
public override Equals(T other) {
	if (other is null) {
		return false;
	}

	/* some other checks */
}
```

### Не переопределен `T.Equals(object)`

Вы переопределяете `T.Equals(T)`, но не переопределяете выописанный метод, который вызывается по дефолту. То, есть лучше всего дописать вот такою конструкцию:
```C#
public override bool Equals(object o) {
	if (o is null) {
		return false;
	} else if (o is T t) {
		return this.Equals(t):
	} else {
		return false;
	}
}
```

## Строки
### Форматирование 
Используйте конструкцию `$"text"` для форматирования строк.

### Сложение
Избегайте сложения строк. Вот такой вот код создает сразу 11 строк:
```C# 
public override string ToString()
{
	return
		$"Record id: {ID}\n" +
		$"Author id: {AuthorId}\n" +
		$"Title: {Title}\n" +
		$"Text: {Text}";
}
``` 
Во первых, на каждую строку для форматирования создается две строки: первая для форматирования, вторая как результат. Это уже восемь. Потом, при сложении, старые строки не удаляются из памяти просто так, и так получается еще 3. В сумме — 11.
Тут лучше было сделать форматирование строк с помощью одной строки для форматирования или StringBuilder'а. Почему так читайте [тут](https://stackoverflow.com/questions/1972983/string-concatenation-vs-string-builder-append).

### Сравнение
[Читать гайды.](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/strings/how-to-compare-strings)

## IEnumerable
Во-первых, стоит реализовывать, не просто `IEnumerable`, а `IEnumerable<out T>`. При реализации первого, в `foreach` у вас будут просто объекты, а не объекты нужного типа.
Во-вторых, делайте это правильно. Вот пример моей реализации на yield'ах для кода Лизы:
```C#
 public IEnumerator GetEnumerator() {
	return GetEnumerator();
 }

IEnumerator<Book> IEnumerable<Book>.GetEnumerator() {
	foreach (Book book in _list) {
		yield return book;
	}
}
```
Как видите, здесь нет никаких кастов типов, это все гарантировано работает. Как вариант, можно было сдеать вот так:
```C#
public IEnumerator<MovieShow> GetEnumerator() => _list.GetEnumerator;

public IEnumerator IEnumerable.GetEnumerator() => _list.GetEnumerator;
```

[1]: https://msdn.microsoft.com/en-us/library/336aedhh(v=vs.100).aspx