
## Dropping from DataFrame

### Drop mask for DataFrames

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
```

### Drop using mask 

Using the previously created disallow_values_per_protocol we can now create a list of unwanted values and later drop them from the df

``` python
disallow_indices = set()
for protocol, rel_cols in rel_cols_per_protocol.items():
	for col in rel_cols:
	df = df_network[(df_network.packet_protocol == protocol)]
	disallow_indices.update(df[(df[col].isin(disallow_values_per_protocol[protocol]))].index)
```

