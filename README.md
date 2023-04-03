---
license: other
---

## Prepraration

```
pip3 install -r requirements.txt
```

## Data Cleaning

1. merge two raw json files and json beautify the merged file

```
python merge.py sharegpt_90k_raw_dataset/sg_90k_part1.json sharegpt_90k_raw_dataset/sg_90k_part2.json  sharegpt_20230401_html_unformatted.json
python pretty_json.py --in sharegpt_20230401_html_unformatted.json --out sharegpt_20230401_html.json
```

2. (Optional) Verify the json file

```
if jq empty sharegpt_20230401_html.json 2>/dev/null; then
  echo "JSON is valid"
else
  echo "JSON is invalid"
fi

jq length sharegpt_90k_raw_dataset/sg_90k_part1.json
jq length sharegpt_90k_raw_dataset/sg_90k_part2.json
jq length sharegpt_20230401_html.json
```

3. clean data - remove html tags etc

```
python3 clean_sharegpt.py --in sharegpt_20230401_html.json --out sharegpt_20230401_clean.json
....
100%|███████████████████████████████████████████████████████████████████| 90665/90665 [06:32<00:00, 230.98it/s]
total: 90665, skip: 13745, new: 76920
```

4. Filter dataset by language

```
python3 optional_clean.py --in sharegpt_20230401_clean.json --out sharegpt_20230401_clean_lang_zh.json --lang zh
....
return 6240 out of 76920, start dump ...

python3 optional_clean.py --in sharegpt_20230401_clean.json --out sharegpt_20230401_clean_lang_en.json --lang en
...
return 55413 out of 76920, start dump ...
```

> Note: the code itself doesn't support languange list, I didn't change the code for adpation. You can change the code to support more languages. Instead, I just filter two languages I need and merge the `sharegpt_20230401_clean_lang_zh.json` and `sharegpt_20230401_clean_lang_en.json` into `sharegpt_20230401_clean_lang.json`. 


5. Split the long conversation

```
python3 split_long_conversation.py --in sharegpt_20230401_clean_lang.json --out sharegpt_20230401_clean_lang_split.json --model-name /home/ubuntu/llama-13b-hf/
...
total: 61653, new: 126032
```

Ok, now we have the cleaned dataset `sharegpt_20230401_clean_lang_split.json` which should be used for finetuning.
