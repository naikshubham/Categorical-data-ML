# Categorical data

#### Ordinal vs Nominal variables
- Ordinal : Categorical vars that have a natural order e.g `Strongly Disagree, Disafree, Neutral, Agree, Strongly Agree`
- Nominal : Categorical vars that cannot be placed into a natural order `Blue, Green, Red, Yellow, Purple`
- A dtype of `object` is how pandas stores strings and is a good indicator that a variable might be categorical.

### Categorical data in pandas
- Default dtype : `object`

#### Set as categorical
- We can convert `object` to categorical dtype using the `astype` method and specifying `category`.

```python
adult['Marital Status'] = adult['Marital Status'].astype('category')
adult['Marital Status'].dtype
```

- Pandas is now telling us that the var is now saved as categorical and is providing the list of the categories found in the Series.

#### Creating a categorical series

```python
# 1st way
my_data = ['A', "A", 'C', "B", "C", "A"]
my_series1 = pd.Series(my_data, dtype='category')
print(my_series1)

# 2nd way
my_series2 = pd.Categorical(my_data, categories=["C","B","A"], ordered=True)
```

- This allows us to tell pandas that the categories have a logical order by setting the `ordered argument equal True`. The order is set using the categories parameter. Whichever order we list the categories in will be the order of the categories going forward.

### Why do we use categorical : memory
- There are few reasons why storing pandas series with a dtype of categorical is useful : **it's a huge memory saver**

```python
adult = pd.read_csv('data/adult.csv')
adult['Marital Status'].nbytes

adult['Marital Status'] = adult['Marital Status'].astype('category')
adult['Marital Status'].nbytes
```

- Since pandas will by default load all the data into our computers memory, reducing memory footprint can be helpful when dealing with large datasets.

#### Specify dtypes when reading data
- If we know the data types of columns before reading in a dataset, it is good practise to specify atleast some of the columns dtypes. This can be done by creating dictionary with column names as keys and data types as values.

```python
adult_dtypes = {"Marital Status":"category"}
adult = pd.read_csv("data/adult.csv", dtype=adult_dtypes)
```

### Grouping data by category in pandas

```python
adult = pd.read_csv('data/adult.csv')
adult1 = adult[adult['Above/Below 50k'] == '<=50K']
adult2 = adult[adult['Above/Below 50k] == '>50k']

# is replaced by

groupby_object = adult.groupby(by=['Above/Below 50k'])

# specifying columns

adult.groupby(by=["Above/Below 50k"])['Age', 'Education Num'].sum()
adult.groupby(by=["Above/Below 50k",'Marital Status']).size()
```

### Setting category variables
- To get the most out of using the pandas categorical dtype, we need to understand how to set, add and remove categories.

```python
dogs['coat'] = dogs['coat'].astype('category')
dogs['coat'].value_counts(dropna=False)
```

#### The .cat accessor object
- This object let's us access and manipulate the categories of a categorical series.
- `Series.cat.method_name` 
- Common parameters : `new_categories` : a list of categories
- `inplace` : Boolean - whether or not the update should overwrite the Series
- `ordered` : Booelan - whether or not the categorical is treated as an ordered categorical.

#### Setting Series categories

```python
dogs['coat'] = dogs['coat'].cat.set_categories(new_categories=["short","medium","long"])
# check value counts

dogs['coat'].value_counts(dropna=False)
```

#### Setting order
- We can set the order of the categories using the ordered parameter.

```python
dogs['coat'] = dogs['coat'].cat.set_categories(new_categories=['short','medium','long'], ordered=True)
dogs['coat'].head(3)
```

#### Adding categories
- We can add categories using `cat.add_categories` method

```python
dogs['likes_people'] = dogs['likes_people'].astype('category')
dogs['likes_people'] = dogs['likes_people'].cat.add_categories(new_categories=['did not check", "could not tell"])
dogs['likes_people'].cat.categories # shows categories
```

- Although we added categories, this doesn't mean any rows of our data were set to these categories

#### Remove categories
- `cat.remove_categories`

```python
dogs['coat'] = dogs['coat'].astype("category")
dogs['coat'] = dogs['coat'].cat.remove_categories(removals=["wirehaired"])
```

#### Updating categories

##### Renaming categories
- supply key val pairs with key being current category and value being the desired category, we can rename categories quickly. `Series.cat.rename_categories(new_categories=dict)`
- make a dict : my_changes = {'unknown mix':'Unknown'}
- If we need to collapse categories, the `.replace()` method is quick and easy, but we will need to convert the column back to categorical.

```python
dogs['breed'] = dogs['breed'].cat.rename_categories(my_changes)
dogs['breed'].value_counts()

# renaming using lambda functions
dogs['sex'] = dogs['sex'].cat.rename_categories(lambda c:c.title())
dogs['sex'].cat.categories
```

#### Common replacement issues
- First, the new category must not currently be in the list of categories. Second we cannot use this method to collapse categories.

```python
cannot_repeat_categories = {
    'Unknown Mix':'Unknown',
    'Mixed Breed':'Unknown'
    }
```

#### Collapsing categories
- Dogs hair many different colors, so it might make sense to make new categorical column that just has hair color.

```python
dogs['color'] = dogs['color'].astype('category')
dogs['color'].cat.categories
```










