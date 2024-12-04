# 云游 OCPX 对接文档

#### 更新日志

| 版本    | 时间         | 修订内容                               |
|:------|:-----------|:-----------------------------------|
| 1.1.0 | 2024-10-10 | 弃用from to参数；添加链接唯一标识id，TrackLinkId |
| 1.0.3 | 2024-09-09 | 添加其它未定义事件；相关事件说明                   |
| 1.0.2 | 2024-08-26 | 优化参数                               |
| 1.0.1 | 2024-07-18 | 添加caid、caid1_md5字段；添加unionSite重要说明 |
| 1.0.0 | 2024-07-11 | 添加曝光上报                             |

## 一、接口说明

`链接类型`：

- **媒体链接**：需向运营或商务申请快手、趣头条、头条、厂商等其他媒体的专属链接。上报链路为：媒体 -> 云游（预算方）。

- **ADX链接**：在对接此OCPX文档后，申请ADX链接，并对监测链接进行宏替换。上报链路为：媒体 -> 渠道 -> 云游（预算方）。

GET 请求
默认只需点击上报
点击上报：https://tracking.oneway.mobi/api/activate

`（如需曝光上报则使用此接口；禁止将曝光上报到点击接口！默认仅点击上报，如需曝光上报会告知）`

曝光上报：https://tracking.oneway.mobi/api/activate/show

1. 监测数据上报接口

#### 1.2 请求参数

| 参数名            | 宏                                  | 类型     | 参数意义说明                                | 是否必填 |
|:---------------|:-----------------------------------|--------|---------------------------------------|------|
| from           | 此字段自2024/10/10日起弃用，旧链接仍然生效，新链接无此字段 | String | 点击来源。弃用！                              | 是    |
| to             | 此字段自2024/10/10日起弃用，旧链接仍然生效，新链接无此字段 | String | 产品id。弃用！                              | 是    |
| ch             | 会提供                                | String | ch链接号。                                | 是    |
| trackLinkId    | 会提供                                | String | 链接ID。链接唯一标识。                          | 是    |
| event_type     | 会提供                                | String | 如预算无具体转化事件，此字段则为默认回传转化事件              | 是    |
| tid            | \_\_TID\_\_                        | String | 跟踪 id。唯一标识。建议使用 UUID                  | 是    |
| callback_url   | \_\_CALLBACK_URL\_\_               | String | 回传地址；回传发送 get 请求。详见下方重要说明             | 是    |
| idfa           | \_\_IDFA\_\_                       | String | IOS 必传其一                              | 是    |
| idfa_md5       | \_\_IDFA_MD5\_\_                   | String | IOS 必传其一                              | 是    |
| caid1          | \_\_CAID1\_\_                      | String | IOS 必传其一。跑uc、夸克请勿使用此字段                | 否    |
| caid1_md5      | \_\_CAID1_MD5\_\_                  | String | IOS 必传其一。跑uc、夸克请勿使用此字段                | 否    |
| caid           | \_\_CAID\_\_                       | String | json格式。详见下方重要说明。跑uc、夸克IOS必传其一，否则无法归因。 | 是    |
| imei_md5       | \_\_IMEI_MD5\_\_                   | String | 安卓必传其一                                | 是    |
| imei           | \_\_IMEI\_\_                       | String | 安卓必传其一                                | 是    |
| oaid           | \_\_OAID\_\_                       | String | 安卓必传其一                                | 是    |
| oaid_md5       | \_\_OAID_MD5\_\_                   | String | 安卓必传其一                                | 是    |
| android_id     | \_\_ANDROID_ID\_\_                 | String | 推荐。有要求时必填                             | 否    |
| android_id_md5 | \_\_ANDROID_ID_MD5\_\_             | String | 推荐。有要求时必填                             | 否    |
| ua             | \_\_UA\_\_                         | String | 移动设备 UA 推荐填写                          | 否    |
| ip             | \_\_IP\_\_                         | String | IP 地址 推荐填写                            | 否    |
| ipv6           | \_\_IP6\_\_                        | String | ipv6 地址 推荐填写                          | 否    |
| event_time     | \_\_EVENT_TIME\_\_                 | String | 点击上报时间戳 ms 强烈推荐填写！                    | 否    |
| cid            | \_\_CREATIVE_ID\_\_                | String | 创意 ID 推荐     `uc、夸克必填`                | 否    |
| adgroup_id     | \_\_ADGROUP_ID\_\_                 | String | 广告组 ID 推荐   `uc、夸克必填`                 | 否    |
| campaign_id    | \_\_CAMPAIGN_ID\_\_                | String | 广告计划 ID 推荐   `uc、夸克必填`                | 否    |
| account_id     | \_\_ACCOUNT_ID\_\_                 | String | 广告投放账户 ID 推荐                          | 否    |
| model          | \_\_MODEL\_\_                      | String | 设备机型。要求时必填                            | 否    |
| cb_ext         |                                    | String | 预留参数                                  | 否    |

#### 1.3 请求参数重要说明

- idfa、idfa_md5、caid、caid1；iOS设备ID参数必填其中一个，且是有效值。传默认值则会影响归因（默认值如：0000-0000这种）
  </br> 需要将获取后的 json 进行
  encode。必须encode，且不能为两次。<br/>`[{"version":"caid版本号","caid":"CAID原始值"},{"version":"caid版本号","caid_md5":"CAIDMD5值"}]`
- imei、imei_md5、oaid、oaid_md5；安卓设备ID参数必填其中一个，且是有效值。传默认值则会影响归因（默认值如：0000-0000这种）
- `ip、ua 尽可能传!`。归因途径一般为：设备id>ip>ua

- `callback_url参数必填。支持自定义参数，发生转化时我方会对callback_url进行宏替换。流量方在callback_url中拼接如下参数，参数可自定义，宏不可更改。`
  </br> `示例：&clickId=__CLICKID__&eventType=__EVENT_TYPE__`
  </br> `其中__CLICKID__为跟踪ID；__EVENT_TYPE__为转化类型参数。详见下方event_type事件说明`
- event_type 参数说明：
    - 1：激活拉新
    - 2：注册
    - 3：⾸唤拉活
    - 4：付费
    - 5：次日留存
    - 51：3日留存
    - 52：4日留存
    - 53：5日留存
    - 54：6日留存
    - 6：7天留存
    - 61：30日留存
    - 7：下载
    - 8：APP内访问
    - 9：召回
    - 10：关键行为（黑盒行为，用户的关键行为，涉及质量分等;`重要`）
    - 11：首购
    - 12：唤端
    - 13：有效唤端
    - 14：下单
    - 99：自定义预留事件，如有自定义事件，请同步
    - 100：预算未定义（未知事件尚未明确同步，如已明确则会在群中同步）

#### 1.4 响应参数

| 参数名     | 类型      | 说明                 | 是否必填 |
|---------|---------|--------------------|------|
| success | boolean | true 代表上报成功，其它代表失败 | 是    |
| message | String  | 失败原因               | 是    |

#### 1.5 请求成功响应

```json
{
  "success": true,
  "message": "success"
}
```
