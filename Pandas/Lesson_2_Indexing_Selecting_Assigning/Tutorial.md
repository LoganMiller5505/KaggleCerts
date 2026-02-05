Can access the property of an object by accessing it as an attribute.

Ex: `book` object has `title` property, which you access using `book.title`. Columns in DataFrame work similarly.

Can access a dictionary's values using indexing []. Same works for DataFrames

Ex: `book[title]`

Indexing on [] has an advantage over attribute accessing, since it won't break against reserved characters (ex: spaces)

You can access a single object in the returned Pandas series just like its a dictionary, using a secondary indexing operator

Ex: `book[title][0]`

---
Pandas also has its own accessor operations, `loc` and `iloc`.

---
Index-based selection is `iloc`. To select the first row of a dataframe, use: `book.iloc[0]`

These operations are row-first, column-second. This means its easier to retrieve rows and harder to get columns. To get a column, use: `book.iloc[:, 0]`

The : operator means "everything" by itself, but can also indicate a range of values.

Ex: To select the first 3 rows and column 0, use `book.iloc[:3, 0]`. To select the second and third entires for column 0, use `book.iloc[1:3,0]`

You also can pass a list, which is equivalent

Ex: `book.iloc[[0,1,2],0]`

Negative numbers can also be used to start counting from the end of the values.

Ex: To select the last 5 elements, use `book.iloc[-5:]`

---
Label-based selection is `loc`. Here, the index value matters, not the position.

Ex: To select the first entry in book, use `book.loc[0, 'title']`

---
iloc 0:10 will select entries 0, ..., 9

loc 0:10 will select entires 0, ..., 10

This is because loc can index any standard python type, so it needs to be inclusive, unlike the more Python-standard iloc

---
The index used is NOT immutable. The set_index() method will change it

Ex: To change the index of book to the title attribute, use `book.set_index('title')`

---
Can use conditions to ask questions about the data.

Ex: Checking book titles using `book.title == 'Harry Potter'`

This produces a Series of True/False booleans based on the condition. The result can be used inside of loc to select the relevant data entries

Ex: Checking `book.loc[book.title == 'Harry Potter']`

You can bring two questions together using &

Ex: Grabbing entries where the title is Harry Potter and the rating is greater than or equal to 90 using `book.loc[(book.title == 'Harry Potter') & (book.rating)>=90]`

Similar process works for or using |

Ex: Grabbing entries where the title is Harry Potter or the rating is greater than or equal to 90 using `book.loc[(book.title == 'Harry Potter') | (book.rating)>=90]`

---

There are also two built-in conditional selectors

isin lets you select data whose value "is in" a list of values

Ex: Grabbing entries where the title is Harry Potter or Percy Jackson using `book.loc[book.title.isin(['Harry Potter', 'Percy Jackson'])]`

isnull/notnull highlights values which are/are not empty (NaN)

Ex: Filtering books lacking a title using `book.loc[book.title.notnull()]`

---

Assigning data using a constant value

Ex: `book['rating'] = 0`

Or with an iterable of values

Ex: `book['rating'] = 0, range(len(books)), 1)`