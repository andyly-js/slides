---
theme: seriph
layout: cover
background: https://source.unsplash.com/1920x1080/?broccoli
highlighter: shiki
transition: slide-up
title: Unit Testing in DBT
lineNumbers: true
---

# Unit Testing in DBT

## Presented by Broccoli Squad

<!--
This is the introduction to Unit Testing in DBT
-->

---

# The problem 
<br> 

- DBT has unit tests already which look at *data validity* rather than transformation logic. 

- DBT uses {{ ref }} and {{ source }} which makes it difficult to control the input data for built in testing methods. The inability to define what that goes into a test makes it difficult to define our expectations

  ```sql 
  SELECT * 
  FROM {{ ref(some_view) }}
  ```
 
- dbt-unit-testing has functionality which enables us to inject our own defined data and dependencies into the model to perform unit tests. 
  A developer can mock the dependencies that are fed into a model, so that we can create expectations of what results are. 


<!--
Why do we need this when DBT already has testing built in?
-->

---

# Process 
<br> 
dbt-unit-testing has functionality which enables us to inject our own defined data and dependencies into the model to perform unit tests. 

It uses a macro which will swap out the dependencies in a model with mock up of our own. 

The model is then fed with fed into a model, which is compared to an expected output. 

[^1]: [Learn More](https://sli.dev/guide/syntax.html#line-highlighting)

---

# Anatomy of a unit test

There are 4 main parts to building a test:
<br>
Boiler plate -> define test -> mock ref/source -> expect   

```python{all|1|3,16|4-10|12-14}
{{ config(tags=['unit-test']) }}

{% call dbt_unit_testing.test ('[Model to Test]','[Test Name]') %}
  {% call dbt_unit_testing.mock_ref ('[model name]') %}
     select ...
  {% endcall %}

  {% call dbt_unit_testing.mock_source('[source name]') %}
    select ...
  {% endcall %}

  {% call dbt_unit_testing.expect() %}
    select  ...
  {% endcall %}

{% endcall %}
```

---
layout: center
---

# Let's look at some code 

---

# Caveats 

- Tricky to debug if things go wrong. 
- Parsing values as separators 
- Compilation failures from comments in code 
- Not very DRY
