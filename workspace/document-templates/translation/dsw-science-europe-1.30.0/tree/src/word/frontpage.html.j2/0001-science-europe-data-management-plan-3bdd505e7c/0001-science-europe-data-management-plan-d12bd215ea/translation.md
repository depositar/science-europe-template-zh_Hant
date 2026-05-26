# Translation Unit

- Source File: `src/word/frontpage.html.j2`
- Wrapper Name: `__tr_block_0000`
- Wrapper Order: `1`
- Wrapper Key: `science-europe-data-management-plan-3bdd505e7c`
- Unit Key: `science-europe-data-management-plan-d12bd215ea`
- Source Hash: `5f027f4f6339aec740fadcb5740e98e9ccc492ed`
- Edit only the `Translation (zh_Hant)` block below.

### Sentence (en)

```text
Science Europe. Data Management Plan. {version.name}.
```

### Translation (zh_Hant)

~~~jinja
<h1>
<strong>Science Europe</strong>
<br/>
<strong>資料管理計畫</strong>
<br/>
<br/>
{% if dc.project.version %}
{% for version in dc.project.versions if version.uuid == dc.project.version.uuid %}
{version.name}
<br/>
{% endfor %}
{% endif %}
~~~
