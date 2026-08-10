# Hierarchical Chinese Web Token Dataset

This repository provides the full release of the hierarchical Chinese web-token dataset described in the accompanying paper.

The dataset contains **630,684 distinct token records**. Among them, **503,497 tokens** are organized into **92,972 hierarchical token trees**, while **127,187 tokens** without a tree relation are released as singleton records. Dataset-level counts refer to distinct token strings.

## Content Warning

The dataset contains tokens, web evidence, and short explanations related to adult content, online gambling, online gaming, online video, and anomalous web text. These materials are released only for research and should be handled with care.

## Files

- `hierarchical_chinese_web_token_trees.jsonl.gz`: hierarchical token trees. Each decompressed line is one tree record.
- `hierarchical_chinese_web_token_singletons.jsonl.gz`: tokens not included in a hierarchical tree. Each decompressed line is one single-node record.
- `tree_structure_visualization.ipynb`: Jupyter notebook for inspecting records and visualizing tree structures.
- `README.md`: this documentation file.

## Data Format

Both data files use gzip-compressed JSONL. Each decompressed line is a JSON object with the same main fields:

- `collection_id`: record identifier within this release.
- `tree_id`: internal tree identifier; `null` for singleton records.
- `collection_type`: tree or singleton record type.
- `root`: root token and its English category label.
- `target_label`: target category represented by the record.
- `is_pure_tree`: whether all tokens in the record belong to the target category.
- `labels_in_tree`: category labels appearing in the record.
- `token_counts`: counts of distinct tokens in the record, including target-category and excluded non-majority tokens.
- `composition`: optional metadata for documented token-composition cases.
- `hierarchical_tree`: recursive token-tree structure.
- `representative_token`: token selected to represent the target category.
- `classification_reason`: concise English explanation of the record-level category assignment; `null` when suitable web evidence is unavailable.

The six category labels are `Adult Content`, `Online Gambling`, `Online Video`, `Online Gaming`, `Anomalous`, and `Normal Content`.

## Hierarchical Tree Structure

The `hierarchical_tree` field is recursive. Each node contains:

- `token`: token string.
- `label`: English category label.
- `depth`: node depth, with the root at depth 0.
- `reason_covered`: whether the node belongs to the target category covered by the record-level explanation.
- `web_context`: retrieved web evidence associated with the token; `null` when unavailable.
- `children`: child nodes.

The nested `children` fields preserve the hierarchical token structure rather than representing each tree as a flat token list. Impure trees retain non-majority nodes, mark them with `reason_covered: false`, and do not assign separate explanations to those nodes.

## Representative Tokens and Explanations

Each tree has one representative token for its target category and one corresponding classification reason derived from the representative token and its web evidence. For singleton records, the token itself is the representative token.

When no suitable evidence is available, the representative token is retained but `classification_reason` is `null`. This applies to 149 tree records and 629 singleton records.

## Composition Cases

Two records include optional `composition` metadata for cases where normal component tokens form an online-gambling token in context:

- In tree `46049`, `菲律宾` and `申博` form `菲律宾申博`.
- In tree `62372`, `北京` and `赛车` form `北京赛车`.

## Visualization

Place the notebook and tree data file in the same directory, open `tree_structure_visualization.ipynb` in VS Code, Jupyter Notebook, or JupyterLab, and run all cells.

To inspect the composition cases by their featured token or current tree identifier, use:

```python
display_tree("菲律宾申博", max_depth=3)
display_tree(62372, max_depth=4)
```

Use `max_depth=None` to expand the complete tree.

## Release Notes

This full release supersedes the 20-tree anonymous-review sample. Record identifiers are internal to this release and should not be used to match records across different dataset versions.
