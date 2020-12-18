# Data for the contest

## introduction

All datasets are csv files.

### 1. annual_report_info.csv

包含数据集7和8中涉及到的企业的年报基本信息，每一行代表一个企业的年报基本数据，每一行有23列，其中id列为企业唯一标识，列之间采用“,”分隔符分割。

数据格式如下：

|   列名   |  含义    |   列名   |   含义   |
| ---- | ---- | ---- | ---- |
| id | 企业唯一标识 | ANCHEYEAR | 年度 |
| STATE | 状态 | FUNDAM | 资金数额 |
| MEMNUM | 成员人数 | FARNUM | 农民人数 |
| ANNNEWMEMNUM | 本年度新增成员人数 | ANNREDMEMNUM | 本年度退出成员人数 |
| EMPNUM | 从业人数 | EMPNUMSIGN | 从业人数是否公示 |
| BUSSTNAME | 经营状态名称 | COLGRANUM | 其中高校毕业生人数经营者 |
| RETSOLNUM | 其中退役士兵人数经营者 | DISPERNUM | 其中残疾人人数经营者 |
| UNENUM | 其中下岗失业人数经营者 | COLEMPLNUM | 其中高校毕业生人数雇员 |
| RETEMPLNUM | 其中退役士兵人数雇员 | DISEMPLNUM | 其中残疾人人数雇员 |
| UNEEMPLNUM | 其中下岗失业人数雇员 | WEBSITSIGN | 是否有网站标志 |
| FORINVESTSIGN | 是否有对外投资企业标志 | STOCKTRANSIGN | 有限责任公司本年度是否发生股东股权转让标志 |
| PUBSTATE | 公示状态：1 全部公示，2部分公示，3全部不公示|

#### 特征处理

- 删除缺失值过多的列
- 按照id分组后聚合
  - ANCHEYEAR: max
  - STATE: max
  - FUNDAM: max
  - EMPNUM: max
  - UNEEMPLNUM: max, sum
  - RETEMPLNUM: max
  - COLEMPLNUM: max
  - COLGRANUM: max

### 2. base_info.csv

包含数据集7和8中涉及到的所有企业的基本信息，每一行代表一个企业的基本数据，每一行有33列，其中id列为企业唯一标识，列之间采用“,”分隔符分割。

数据格式如下：

|   列名   |  含义    |   列名   |   含义   |   列名   |  含义    |   列名   |   含义   |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| id | 企业唯一标识 | oplocdistrict | 行政区划代码 | industryphy | 行业类别代码 | industryco | 行业细类代码 |
| dom | 经营地址 | opscope | 经营范围 | enttype | 企业类型 | enttypeitem | 企业类型小类 |
| opfrom | 经营期限起 | opto | 经营期限止 | state | 状态 | orgid | 机构标识 |
| jobid | 职位标识 | adbusign | 是否广告经营 | townsign | 是否城镇 | regtype | 主题登记类型 |
| empnum | 从业人数 | compform | 组织形式 | parnum | 合伙人数 | exenum | 执行人数 |
| opform | 经营方式 | ptbusscope | 兼营范围 | venind | 风险行业 | enttypeminu | 企业类型细类 |
| midpreindcode | 中西部优势产业代码 | protype | 项目类型 | oploc | 经营场所 | regcap | 注册资本（金） |
| reccap | 实缴资本 | forreccap | 实缴资本（外方） | forregcap | 注册资本（外方） | congro | 投资总额 |
| enttypegb | 企业（机构）类型 |||||||

#### 特征处理

- 删除缺失值过多的列
- 设置三个新特征：
	- district_FLAG1：行政区划代码oplocdistrict与机构标识orgid二者的前六位是否相同
	- district_FLAG2：orgid与职位标识jobid的前六位是否相同
	- district_FLAG3：oplocdistrict与jobid的前六位是否相同

- 设置两个新特征：
	- person_SUM = empnum + parnum + exenum # 可能有问题
	- person_NULL_SUM： empnum、parnum、exenum三者缺失的个数
- 设置两个新特征：
	- opfrom_TONOW：到当前时刻的经营时间（天）
	- opfrom_TOTIME：从opfrom到opto的经营时间（天）
- 设置特征：
	- opscope_COUNT：经营范围种类总数，顿号改成逗号，然后按逗号分词

- 将分类特征['oplocdistrict', 'industryphy', 'industryco', 'enttype', 'enttypeitem', 'enttypeminu', 'enttypegb', 'dom', 'oploc', 'opform']的类别用整型代替，出现次数小于10的类别统一为一类
- 删除opfrom、opto两列

### 3. change_info.csv

包含数据集7和8中涉及到的企业的变更信息，每一行代表一个企业变更信息，每一行5列，其中id列为企业唯一标识，列之间采用“,”分隔符分割。

数据格式如下：

| 列名 | 含义         | 列名   | 含义         |
| ---- | ------------ | ------ | ------------ |
| id   | 企业唯一标识 | bgxmdm | 变更信息代码 |
| bgq  | 变更前       | bgh    | 变更后       |
| bgrq | 变更日期     |        |              |

#### 特征处理

- 变更日期只用年份表示
- 按照id分组后聚合
	- bgxmdm: count, nunique
	- bgq: nunique
	- bgh: nunique
	- bgrq: nunique

### 4. news_info.csv

包含数据集7和8中涉及到的企业的新闻舆情信息，每一行代表一个企业新闻舆情，每一行3列，其中id列为企业唯一标识，列之间采用“,”分隔符分割。

数据格式如下：

| 列名        | 含义         | 列名             | 含义         |
| ----------- | ------------ | ---------------- | ------------ |
| id          | 企业唯一标识 | positive_negtive | 新闻正负面性 |
| public_date | 发布日期     |                  |              |

#### 特征处理

- 把所有非标准格式的时间用当前时间代替
- 发布日期用距今的天数表示（int）
- 按id分组后对发布日期聚合形成新特征：
	- public_date_COUNT: count
	- public_date_MAX: max
	- public_date_MIN: min
	- public_date_MEAN: mean

- 统计各个企业新闻的态度，设置新特征news_COUNT1、news_COUNT2、news_COUNT3，表示积极、中立、消极的新闻数量

### 5. other_info.csv

包含数据集7和8中涉及到的企业的其他信息，每一行代表一个企业其他信息，每一行4列，其中id列为企业唯一标识，列之间采用“,”分隔符分割。

数据格式如下：

| 列名      | 含义         | 列名               | 含义         |
| --------- | ------------ | ------------------ | ------------ |
| id        | 企业唯一标识 | legal_judgment_num | 裁判文书数量 |
| brand_num | 注册商标数量 | patent_num         | 专利数量     |

#### 特征处理

- 删除重复项
- 设置两个新特征：
	- other_SUM = legal_judgment_num + brand_num + patent_num
	- other_NULL_SUM： legal_judgment_num、brand_num、patent_num三者缺失的个数

### 6. tax_info.csv

包含数据集7和8中涉及到的企业的纳税信息，每一行代表一个企业的纳税信息，每一行有9列，其中id列为企业唯一标识，列之间采用“,”分隔符分割。

数据格式如下：

| 列名       | 含义         | 列名           | 含义     |
| ---------- | ------------ | -------------- | -------- |
| id         | 企业唯一标识 | START_DATE     | 起始时间 |
| END_DATE   | 终止时间     | TAX_CATEGORIES | 税种     |
| TAX_ITEMS  | 税目         | TAXATION_BASIS | 计税依据 |
| TAX_RATE   | 税率         | DEDUCTION      | 扣除数   |
| TAX_AMOUNT | 税额         |                |          |

#### 特征处理

- 按id分组聚合
	- TAX_CATEGORIES: count
	- TAX_ITEMS: count
	- TAXATION_BASIS: count
	- TAX_AMOUNT: max, min, mean

### 7. entprise_info.csv

带标注的企业数据。每一行代表一个企业，每一行2列，其中id列为企业唯一标识，label列为标注（1：有非法集资风险，0：无非法集资风险），列之间采用“,”分隔符分割。

### 8. entprise_evaluate.csv

未标注企业数据。参赛队伍需提交的最终结果数据集，每一行代表一个企业，每一行有 2 列, 其中id列为企业唯一标识，score列为空，列之间采用“,”分隔符分割。