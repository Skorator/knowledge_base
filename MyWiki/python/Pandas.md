
## Dropping from DataFrame

## Selection
### Selecting rows 

#### loc()

```python
df.loc[train['class'] == 1, 'class2'] = 1
```
#### Conditional selection without .loc() - [see](https://stackoverflow.com/questions/17071871/how-do-i-select-rows-from-a-dataframe-based-on-column-values)

```python
df.loc[df['column_name'] == some_value]
```

To select rows whose column value is in an iterable, `some_values`, use `isin`:

```python
df.loc[df['column_name'].isin(some_values)]
```

Combine multiple conditions with `&`:

```python
df.loc[(df['column_name'] >= A) & (df['column_name'] <= B)]
```

# Dropping
### Masked dropping

``` python
relevant_columns_per_protocol: dict = {
	'wifi': ['eth_src', 'eth_dst'],
	'bluetooth': ['bluetooth_src', 'bluetooth_dst'],
}

disallow_values_per_protocol: dict = {}

for protocol, rel_cols in rel_cols_per_protocol.items():
	disallow_values_per_protocol[protocol] = set()
	for col in rel_cols:
		disallow_values_per_protocol[protocol].update(
			df_noise[(df_noise.packet_protocol == protocol)][col].unique(9)
		)
		
disallow_indices = set()
for protocol, rel_cols in rel_cols_per_protocol.items():
	for col in rel_cols:
	df = df_network[(df_network.packet_protocol == protocol)]
	disallow_indices.update(df[(df[col].isin(disallow_values_per_protocol[protocol]))].index)
```

## Replace values

### Using matching condition and .loc()
```python
train.loc[train['Pclass'] == 1, 'Cabin'] = 1
```

### Map values to values in column: 

- Replace values in `df["dst"]` that have **value "key"** with the dictionary's **"value"**
```python
mac_mapping = {
	"0xfffc": "0xfffc\nBC_to_coord",
	"0xb37a":"0xb37a\bAqara Socket",
	"0x4f8e":"0x4f8e\n???",
	"0xa465":"0xa465\nSonoff Switch",
	"0x3417":"0x3417\nSqara Switch",
}
df_comm["dst"].replace(mac_mapping, inplace=True)
```

### Add count column ([see](https://stackoverflow.com/questions/35268817/unique-combinations-of-values-in-selected-columns-in-pandas-data-frame-and-count))

- Add a counter to the unique pairs of `Group (A, B)` as a new column
```python
df1.groupby(['A','B']).size().reset_index().rename(columns={0:'count'})
```

## Merging

### Merge values of two columns in python

```python
dfs['Zigbee']['col_new'] = dfs["Zigbee"]['col1'] + dfs["Zigbee"]['col2']
```

### Merge columns in python

Use concat 
