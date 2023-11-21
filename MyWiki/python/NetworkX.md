
## Accessing node attributes

```


```

## Assigning positions to nodes
- Has to be done **AFTER** creating and filling the graph. 
  
```python
  pos = nx.spring_layout(bt_graph, k=1, seed=3113794652) # positions for all nodes 
  pos = nx.spectral_layout(bt_graph)
  pos = nx.circular_layout(bt_graph)
```


