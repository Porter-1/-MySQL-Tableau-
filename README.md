# 客户购物数据分析项目
## 一、项目简介
使用 MySQL 处理客户购物数据，再通过 Tableau 可视化呈现。从用户画像、商品偏好、购买行为到忠诚度分层，全面剖析客户特征，识别高价值用户，为营销策略提供数据支持。

## 二、数据说明
### 数据来源
https://www.kaggle.com/datasets/iamsouravbanerjee/customer-shopping-trends-dataset/data

### 字段说明
| 字段名                | 含义                                                     |
|-----------------------|----------------------------------------------------------|
| Customer_ID           | 客户唯一标识符                                           |
| Age                   | 客户年龄                                                 |
| Gender                | 客户性别(男/女)                                          |
| Item_Purchased        | 客户购买的商品                                           |
| Category              | 购买商品的类别                                           |
| Purchase_Amount       | 购买金额(美元)                                           |
| Location              | 购买地点                                                 |
| Size                  | 购买商品的尺码                                           |
| Color                 | 购买商品的颜色                                           |
| Season                | 购买商品的季节                                           |
| Review_Rating         | 客户对购买商品的评分                                     |
| Subscription_Status   | 客户是否拥有订阅(是/否)                                  |
| Shipping_Type         | 客户选择的配送方式                                       |
| Discount_Applied      | 是否应用了折扣(是/否)                                    |
| Promo_Code_Used       | 是否使用了优惠码(是/否)                                  |
| Previous_Purchases    | 客户在该商店的历史购买总数（不包括当前交易）             |
| Payment_Method        | 客户最常用的支付方式                                     |
| Frequency_of_Purchases| 客户购买频率(每周、每两周、每月等)                       |

### 数据示例
![数据示例](https://github.com/user-attachments/assets/1e3eadd3-d540-4bd8-98a9-9a16805e9f03)

## 三、数据处理
### 数据导入
通过 Navicat 连接云端数据库，导入下载的文件（文件类型选择 csv utf-8），表格命名为 `shopping_trends`。

![数据导入](https://github.com/user-attachments/assets/78b159d6-02ab-4e1c-9c88-8ecd7ca9313a)

### 数据预处理
1. 查看数据：
```sql
SELECT * FROM shopping_trends LIMIT 10;
```
<img width="1964" height="530" alt="image" src="https://github.com/user-attachments/assets/2faf9cf6-a9d6-427b-a3c2-23727ab01db2" />

2. 空值检测及处理：
```sql
SELECT 
  *
FROM 
  shopping_trends
WHERE 
  Customer_id IS NULL
OR
  Age IS NULL
OR
  Gender IS NULL
OR 
  Item_Purchased IS NULL
OR 
  Category IS NULL
OR 
  Purchase_Amount IS NULL
OR 
  Location IS NULL
OR 
  Size IS NULL
OR 
  Color IS NULL
OR 
  Review_Rating IS NULL
OR 
  Subscription_Status IS NULL
OR 
  Payment_Method IS NULL
OR 
  Shipping_Type IS NULL
OR 
  Discount_Applied IS NULL
OR 
  Promo_Code_Used IS NULL
OR 
  Previous_Purchases IS NULL
OR 
  Preferred_Payment_Method IS NULL
OR 
  Frequency_of_Purchases IS NULL;
```
<img width="2044" height="174" alt="image" src="https://github.com/user-attachments/assets/e0541c53-ed19-4732-846f-e251dda21c29" />

分析：无空值，无需处理。

3. 重复值检测及处理：
```sql
SELECT
  Customer_id,
  Age,
  Gender,
  Item_Purchased,
  Category,
  Purchase_Amount,
  Location,
  Size,
  Color,
  Review_Rating,
  Subscription_Status,
  Payment_Method,
  Shipping_Type,
  Discount_Applied,
  Promo_Code_Used,
  Previous_Purchases,
  Preferred_Payment_Method,
  Frequency_of_Purchases
FROM
  shopping_trends
GROUP BY
  Customer_id,
  Age,
  Gender,
  Item_Purchased,
  Category,
  Purchase_Amount,
  Location,
  Size,
  Color,
  Review_Rating,
  Subscription_Status,
  Payment_Method,
  Shipping_Type,
  Discount_Applied,
  Promo_Code_Used,
  Previous_Purchases,
  Preferred_Payment_Method,
  Frequency_of_Purchases
HAVING
  COUNT(*) > 1;
```
<img width="2028" height="160" alt="image" src="https://github.com/user-attachments/assets/641426ec-3776-4212-9080-f93eeac1faea" />

分析：无重复值，无需处理。

## 四、数据分析
#### 用户基本特征分析

通过描述性统计和可视化手段，对用户的年龄分布(Age)、性别比例(Gender)及地域集中度地域(Location)进行分析，呈现出用户群体的轮廓特征。

1.年龄分布
```sql
SELECT
  CASE 
    WHEN 
      Age <= 20
    THEN 
      '20岁以下'
    WHEN 
      20 < Age AND Age <= 30
    THEN 
      '21-30岁'
    WHEN 
      30 < Age AND Age <= 40 
    THEN 
      '31-40岁'
    WHEN 
      40< Age AND Age <= 50 
    THEN 
      '41-50岁'
    WHEN 
      50 < Age AND Age <= 60
    THEN 
      '51-60岁'
    ELSE 
      '60岁以上'
  END AS 年龄段,
  COUNT(*) AS cnt 
FROM 
  shopping_trends
GROUP BY 
  年龄段
ORDER BY 
  年龄段 ASC;
```
<img width="346" height="344" alt="image" src="https://github.com/user-attachments/assets/cd2bd906-c2aa-494e-a301-0d2d888f7ea2" />

2.性别比例
```sql
SELECT 
  Gender,
  COUNT(*) AS cnt,
  CONCAT(ROUND(COUNT(*)/SUM(COUNT(*))OVER()*100,1),'%') AS ratio 
FROM 
  shopping_trends
GROUP BY 
  Gender
```
<img width="558" height="194" alt="image" src="https://github.com/user-attachments/assets/c95434c2-768f-4538-9f38-39259d0d64dd" />
<img width="1124" height="1038" alt="image" src="https://github.com/user-attachments/assets/ab33420c-807c-4881-af03-900ed2d00615" />

3.地域集中度
```sql
SELECT 
  Location,
  COUNT(*) AS 客户数,
  SUM(Purchase_Amount) AS 总消费金额,
  ROUND(AVG(Purchase_Amount),1) AS 平均消费金额
FROM
  shopping_trends
GROUP BY 
  Location
ORDER BY 
  客户数 ASC;
```
<img width="820" height="674" alt="image" src="https://github.com/user-attachments/assets/ff017bb8-6227-4140-9822-c0c45ede476b" />
<img width="1092" height="1034" alt="image" src="https://github.com/user-attachments/assets/9c2a91fb-52d4-440e-8522-23264528c999" />

分析：除了20岁以下的用户较少外，年龄分布均衡，男性用户整体多于女性，且平均消费水平均在60$左右。因此，产品的广告文案、视觉风格可偏向中性、成熟、稳重，避免仅针对年轻人的潮流表达。
Montan地区客户数最多，Alaska地区平均消费金额最高，各地区可分析当地用户画像，及时调整产品品类结构与促销策略。

#### 畅销商品分析

1.类别分析
```sql
SELECT
  Category,
  COUNT(*) AS 数量,
  SUM(Purchase_Amount) AS 总销售额
FROM
  shopping_trends
GROUP BY
  Category
ORDER BY
  数量 ASC;
```
<img width="550" height="262" alt="image" src="https://github.com/user-attachments/assets/293d6cb1-bd6f-48ff-a837-0f2413444332" />

分析:Clothing是最畅销的商品类别，且总销售额最高。因此可将Clothing作为主打品类，加大款式更新与库存保障，并围绕其开发搭配商品（如Accessories）或推出季节限定系列。

2.颜色分析
```sql
SELECT
  Color,
  COUNT(*) AS 数量,
  SUM(Purchase_Amount) AS 总销售额,
  ROUND(AVG(Purchase_Amount), 1) AS 平均销售额
FROM
  shopping_trends
GROUP BY
  Color
ORDER BY
  数量 ASC;
```
<img width="690" height="664" alt="image" src="https://github.com/user-attachments/assets/4396183f-1359-4727-aaf5-3c9e34d20451" />
<img width="2212" height="1272" alt="image" src="https://github.com/user-attachments/assets/620a6181-167c-4616-b95c-31185128e80b" />

分析：Olive色系的客户选择最多，Green色系的平均销售额最高。可在商品设计、广告视觉中优先采用这些流行色，并在商品详情页强化颜色筛选与推荐。

3.季节销售趋势分析
```sql
SELECT
  Season,
  COUNT(*) AS 数量,
  SUM(Purchase_Amount) AS 总销售额,
  ROUND(AVG(Purchase_Amount), 1) AS 平均销售额
FROM
  shopping_trends
GROUP BY
  Season
ORDER BY
  数量 ASC;
```
<img width="690" height="260" alt="image" src="https://github.com/user-attachments/assets/257046d9-5d6a-4a62-a30c-0fa642716af9" />
<img width="2210" height="988" alt="image" src="https://github.com/user-attachments/assets/ca77710c-aac7-4a05-971b-b2cf349f10ae" />
<img width="2216" height="676" alt="image" src="https://github.com/user-attachments/assets/1adb4459-15bc-43ac-bae2-dac43852b0c8" />
<img width="2218" height="754" alt="image" src="https://github.com/user-attachments/assets/39bbf09f-9070-47fc-abbc-bc6f1334ebb5" />

分析：季节性销售趋势显示，秋季的销售最为活跃，且不同季节的热销品类与颜色存在差异。可根据季节特点调整主推品类与主推色系，在旺季加大促销力度，在淡季可通过折扣或捆绑销售清理库存。

#### 用户购买行为分析

1.支付方式偏好分析
```sql
SELECT
  Payment_Method,
  COUNT(*) AS 数量,
  CONCAT(ROUND(COUNT(*)/SUM(COUNT(*))OVER()*100,2),'%') AS 占比
FROM 
  shopping_trends
GROUP BY 
  Payment_Method
ORDER BY 
  数量;
```
<img width="616" height="378" alt="image" src="https://github.com/user-attachments/assets/f6f5a2ca-74c5-44a6-9393-d40f1297da04" />
<img width="1104" height="906" alt="image" src="https://github.com/user-attachments/assets/44cb9c57-6bfc-46d6-ae93-5069448450c1" />

2.配送方式偏好分析
```sqlSELECT
  Shipping_Type,
  COUNT(*) AS 数量,
  CONCAT(ROUND(COUNT(*) / SUM(COUNT(*)) OVER () * 100, 2), '%') AS 占比,
  ROUND(AVG(Purchase_Amount), 1) AS 平均金额
FROM
  shopping_trends
GROUP BY
  Shipping_Type
ORDER BY
  数量;
```
<img width="712" height="348" alt="image" src="https://github.com/user-attachments/assets/1dac7db9-c6cf-445a-bd99-400328c36c1e" />
<img width="1102" height="902" alt="image" src="https://github.com/user-attachments/assets/953c1cba-099a-408e-bb81-66e3c60d927d" />

分析：PayPal、Credit Card、Cash是用户常用的支付方式，Free Shipping配送方式使用频率最高。可以优化支付方式的支付体验、同时调整运费策略，提升支付以及配送的服务质量，以提高用户满意度。

3.历史购买次数分析
```sql
SELECT
    CASE
        WHEN previous_purchases = 1 THEN '1次购买'
        WHEN previous_purchases = 2 THEN '1和2次购买'
        WHEN previous_purchases BETWEEN 2 AND 5 THEN '2-5次购买'
        WHEN previous_purchases BETWEEN 6 AND 10 THEN '6-10次购买'
        WHEN previous_purchases > 10 THEN '10+次购买'
    END AS 购买次数,
    COUNT(*) AS 客户数量,
    CONCAT(ROUND(COUNT(*) / SUM(COUNT(*)) OVER() * 100, 2), '%') AS 占比
FROM shopping_trends
GROUP BY 购买次数
ORDER BY 客户数量 DESC;
```
<img width="512" height="296" alt="image" src="https://github.com/user-attachments/assets/309843aa-ccea-45b6-b97e-4b941e755d0c" />
<img width="1110" height="760" alt="image" src="https://github.com/user-attachments/assets/cfa71ff3-57cf-4a1c-982f-8902a5222346" />


4.购买频率分析
```sql
WITH T1 AS (
   SELECT
    Frequency_of_Purchases,
    CASE
        WHEN previous_purchases = 1 THEN '1次购买'
        WHEN previous_purchases = 2 THEN '1和2次购买'
        WHEN previous_purchases BETWEEN 2 AND 5 THEN '2-5次购买'
        WHEN previous_purchases BETWEEN 6 AND 10 THEN '6-10次购买'
        WHEN previous_purchases > 10 THEN '10+次购买'
    END AS 购买次数,
    COUNT(*) AS 客户数量,
    CONCAT(ROUND(COUNT(*) / SUM(COUNT(*)) OVER() * 100, 2), '%') AS 占比
FROM shopping_trends
GROUP BY Frequency_of_Purchases,购买次数
ORDER BY 客户数量 DESC
)
SELECT
    Frequency_of_Purchases ,
    SUM(CASE WHEN 购买次数 = '1次购买' THEN 客户数量 ELSE 0 END) AS '1次购买客户数量',
    SUM(CASE WHEN 购买次数 = '2-5次购买' THEN 客户数量 ELSE 0 END) AS '2-5次购买客户数量',
    SUM(CASE WHEN 购买次数 = '6-10次购买' THEN 客户数量 ELSE 0 END) AS '6-10次购买客户数量',
    SUM(CASE WHEN 购买次数 = '10+次购买' THEN 客户数量 ELSE 0 END) AS '10+次购买客户数量',
    SUM(客户数量) AS 总客户数量
FROM T1
GROUP BY Frequency_of_Purchases
ORDER BY frequency_of_purchases;
```
<img width="1660" height="378" alt="image" src="https://github.com/user-attachments/assets/cb1de8ae-0bdf-4623-9b0e-4e82e77b0a88" />
<img width="1104" height="754" alt="image" src="https://github.com/user-attachments/assets/70012636-4541-42cd-b60d-cd82ebcdca94" />


5.高复购客户分析
```sql
SELECT 
    COUNT(CASE WHEN Previous_Purchases > 5 AND Frequency_of_Purchases IN ('Bi-weekly','Weekly','Fortnightly')THEN Customer_ID ELSE NULL END ) AS 高复购人数,
    CONCAT(ROUND(COUNT(CASE WHEN Previous_Purchases > 5 AND Frequency_of_Purchases IN ('Bi-weekly','Weekly','Fortnightly')THEN Customer_ID ELSE NULL END)/COUNT(*) * 100,2),'%') AS 占比
FROM 
    shopping_trends
```
<img width="382" height="122" alt="image" src="https://github.com/user-attachments/assets/cb7faf81-beec-427d-8380-cd4c72c97688" />
<img width="2218" height="1314" alt="image" src="https://github.com/user-attachments/assets/577ad4b1-2d4b-4918-9c7f-ea20b65ba4aa" />
<img width="2212" height="904" alt="image" src="https://github.com/user-attachments/assets/8841075d-7f3b-43b4-8f65-9e1b39b8038e" />

分析：高复购用户占总用户数的37%，虽然高复购用户在商品类别的选择上差异不大，但在颜色、支付方式、配送方式上均有差异。建议针对高复购用户设计专属权益（如会员等级、专属折扣），提升其粘性；同时通过推送高频品类、订阅服务等方式，引导中低频用户向高复购转化。

6.忠诚度分析：

忠诚度指标体系搭建：

| 具体指标 | 说明 | 权重 |
|---|---|---|
| Previous_Purchases | 历史购买次数，多次购买说明客户对产品与服务已形成习惯，且持续消费。 | 0.25 |
| Frequency_of_Purchases | 购买频率，比Weekly(每周一次)、Bi-Weekly(每两周一次)、Monthly(每月一次)、Every 1 Months(每月一次)、Every 3 Months(每三月一次)、Quarterly(一季一次)、Annually(一年一次)分别编码1-7。 | 0.2 |
| Purchase_Amount | 消费金额，低于于产品消费金额平均值0.8的划分为低消费，在同产品消费金额平均值0.8-1.2之间的划分为中等消费，高于于产品消费金额平均值1.2的划分为高消费，分别编码为1,2,3。 | 0.2 |
| Discount_Applied | 是否使用折扣，使用记0，未使用记1。因普通客户对价格敏感度较低，折扣使用比例可能更低。 | 0.05 |
| Review_Rating | 客户对产品的评分（1-5分）。高分可能消费意愿、消费程度更高。 | 0.15 |
| Subscription_Status | 是否订阅会员，订阅记1，非订阅记0。订阅用户通常具有更高忠诚度和粘度。 | 0.15 |

```sql
  WITH t1 AS (
    SELECT
      Previous_Purchases,
      CASE
        WHEN Frequency_of_Purchases = 'Bi-Weekly' THEN
          7
        WHEN Frequency_of_Purchases = 'Weekly' THEN
          6
        WHEN Frequency_of_Purchases = 'Fortnightly' THEN
          5
        WHEN Frequency_of_Purchases = 'Monthly' THEN
          4
        WHEN Frequency_of_Purchases = 'Every 3 Months' THEN
          3
        WHEN Frequency_of_Purchases = 'Quarterly' THEN
          2
        WHEN Frequency_of_Purchases = 'Annually' THEN
          1
      END AS Frequency_of_Purchases,
      CASE
        WHEN Purchase_Amount < AVG(Purchase_Amount) OVER (PARTITION BY Category) * 0.8 THEN
          1
        WHEN Purchase_Amount BETWEEN AVG(Purchase_Amount) OVER (PARTITION BY Category) * 0.8
          AND AVG(Purchase_Amount) OVER (PARTITION BY Category) * 1.2 THEN
          2
        WHEN Purchase_Amount > AVG(Purchase_Amount) OVER (PARTITION BY Category) * 1.2 THEN
          3
      END AS Purchase_Amount,
      IF(Discount_Applied = 'Yes', 0, 1) AS Discount_Applied,
      Review_Rating,
      IF(Subscription_Status = 'Yes', 1, 0) AS Subscription_Status
    FROM
      shopping_trends
  ),
  t2 AS (
    SELECT
      (Previous_Purchases - MIN(Previous_Purchases) OVER ()) / (MAX(Previous_Purchases) OVER () - MIN(Previous_Purchases) OVER ()) * 100 X1,
      (Frequency_of_Purchases - MIN(Frequency_of_Purchases) OVER ()) / (MAX(Frequency_of_Purchases) OVER () - MIN(Frequency_of_Purchases) OVER ()) * 100 X2,
      (Purchase_Amount - MIN(Purchase_Amount) OVER ()) / (MAX(Purchase_Amount) OVER () - MIN(Purchase_Amount) OVER ()) * 100 X3,
      (Discount_Applied - MIN(Discount_Applied) OVER ()) / (MAX(Discount_Applied) OVER () - MIN(Discount_Applied) OVER ()) * 100 X4,
      (Review_Rating - MIN(Review_Rating) OVER ()) / (MAX(Review_Rating) OVER () - MIN(Review_Rating) OVER ()) * 100 X5,
      (Subscription_Status - MIN(Subscription_Status) OVER ()) / (MAX(Subscription_Status) OVER () - MIN(Subscription_Status) OVER ()) * 100 X6
    FROM
      t1
  ),
  t3 AS (
    SELECT
      X1 * 0.25 + X2 * 0.2 + X3 * 0.2 + X4 * 0.05 + X5 * 0.15 + X6 * 0.15 AS 忠诚度指数
    FROM
      t2
  ) 
 SELECT
  CASE
    WHEN
      忠诚度指数 < 20 THEN
      '忠诚度极低'
    WHEN 忠诚度指数 >= 20
      AND 忠诚度指数 < 40 THEN
      '忠诚度低'
    WHEN 忠诚度指数 >= 40
      AND 忠诚度指数 < 60 THEN
      '忠诚度中等'
    WHEN 忠诚度指数 >= 60
      AND 忠诚度指数 < 80 THEN
      '忠诚度高'
    WHEN 忠诚度指数 >= 80 THEN
      '忠诚度极高'
  END 忠诚度,
  COUNT(*) 客户数量,
  ROUND(COUNT(*)/SUM(COUNT(*))OVER(),3) AS 百分比
FROM
  t3
GROUP BY
  忠诚度
ORDER BY
  FIELD(忠诚度, '数据缺失', '忠诚度极低', '忠诚度低', '忠诚度中等', '忠诚度高', '忠诚度极高');
```
<img width="508" height="296" alt="image" src="https://github.com/user-attachments/assets/393423ed-eb6c-45e2-bcdb-0557e220a03a" />


<img width="2214" height="992" alt="image" src="https://github.com/user-attachments/assets/328213a8-af29-4762-b5ee-e96361616c26" />

分析:
①忠诚度极低（0-20分）为沉睡用户，占比3.1%，他们可能只是尝试性购买，对品牌几乎没有粘性，复购概率极低。
  营销策略：暂不投入高成本，可通过推送大额优惠券或爆款推荐，尝试激活；若长期无反应，可考虑放弃。
  
②忠诚度低（20-40分）为低频用户，占比30.7%，这些用户对价格敏感，品牌忠诚度尚未形成，容易被竞品吸引，但仍有转化潜力。
  营销策略：推送组合折扣、满减活动，鼓励提高客单价；推荐高频品类培养购买习惯；尝试引导订阅会员获取权益。
  
③忠诚度中等（40-60分）为普通活跃用户，占比47.2%，这些用户已形成一定购买习惯，对品牌有基本信任，是维持销售额的中坚力量。
  营销策略：保持关系维护，提供更多基础权益，如积分兑换、满额包邮等，鼓励向高忠诚度升级。
  
④忠诚度高（60-80分）为忠诚粉丝，占比17.9%，大都是品牌的核心用户，复购稳定，愿意为品质支付溢价，且可能自发传播。
  营销策略：提供专属会员等级、生日礼遇、优先购、专属客服等VIP服务；邀请参与新品内测或品牌活动，增强归属感与口碑。
  
⑤忠诚度极高（80-100分）为超级用户，占比1.1%，是品牌的最有价值用户，贡献度高，是口碑传播的核心力量，可能成为品牌大使。
  营销策略：建立一对一维护机制，提供定制化服务、限量款优先权、年度答谢礼；鼓励其分享推荐，给予推荐奖励或联名机会。
