# 复杂繁琐业务代码重构

## 共同点

查询与下载业务代码中基本都有
- selece的sql查询
- 对查询结果进行map封装
- 参数校验

源代码：

```java
@POST  
@Path("newBillList")  
@Consumes({MediaType.APPLICATION_FORM_URLENCODED})  
public Response getst(@FormParam("operator") String operator, @FormParam("projectName") String projectName, ......) throws Exception {  

   String[] operators = null;  
   if (StringUtils.isNotBlank(operator)) {  
      operators = operator.split(",");  
   }  
   String[] projectNames = null;  
   if (StringUtils.isNotBlank(projectName)) {  
      projectNames = projectName.split(",");  
   }  
   if (!StringUtils.isEmpty(overDueStatus) && !"0".equals(overDueStatus)) {  
      if ("1".equals(billStatus)) {  
         throw ApplicationException.custom_action(-1002, "billstatus参数值需设置为0");  
      }  
   }  
   String[] communitys = null;  
   if (StringUtils.isNotBlank(community)) {  
      communitys = community.split(",");  
   }  
   String[] jiaofeiTypes = null;  
   if (StringUtils.isNotBlank(jiaofeiType)) {  
      jiaofeiTypes = jiaofeiType.split(",");  
   }  
   String bls = "";  
   String ble = "";  
   String cashier = "";//是否提交出纳：0：否；1：是  
   if (StringUtils.isNotBlank(pending) && "1".equals(pending)) {  
      bls = DateUtil.getLastDayOfMonth(new Date());  
      billStatus = "0";  
   }  
   if ("3".equals(billStatus)) {  
      cashier = "1";  
      billStatus = "0";  
   }  
   long count1 = 0;  
   List<Object[]> billcounts = billCountDataSQL(operators, projectNames, ......);  
   if ("0".equals(type)) {  
      count1 = (long) (billcounts.get(0) == null ? 0 : Double.valueOf(String.valueOf(billcounts.get(0))));  
   } else {  
      count1 = billcounts.size();  
   }  
   long pageTotal = 0;  
   List<Object[]> bills = billDataSQL(operators, projectNames, ......);  
   pageTotal = (long) Math.ceil((double) count1 / pageSize);  
   List<Map<String, Object>> listJson = new ArrayList<Map<String, Object>>();  
   for (int i = 0; i < bills.size(); i++) {  
      List<Object[]> li = new ArrayList<Object[]>();  
      li.add(bills.get(i));  
      for (Object[] o : li) {  
         Map<String, Object> map = new HashMap<String, Object>();  
         map.put("id", i);  
         map.put("projectName", o[0]);  
         map.put("houseNo", o[1]);  
         long houseCount = 0;  
         if (o[11] != null && "04".equals(o[11].toString())) {  
            if (o[19] != null) {  
               String[] split = o[19].toString().split(",");  
               houseCount = split.length;  
            }  
         }  
         map.put("houseCount", houseCount);  
         map.put("payer", o[27] == null ? (o[2] == null ? "" : o[2]) : o[27]);  
         map.put("payerTel", o[3] == null ? (o[28] == null ? "" : o[28]) : o[3]);  
         map.put("collectionPeriod", o[4]);  
         map.put("billStartTime", o[5]);  
         map.put("billEndTime", o[6]);  
         map.put("name", o[7]);  
         map.put("tenantName", o[8]);  
         map.put("jiaofeiType", o[9] == null ? "" : o[9]);  
         map.put("contractAttribute", o[11]);  
         map.put("billAmount", o[12]);  
         map.put("billPaidAmount", o[13]);  
         map.put("unBillPaidAmount", Math.abs(o[12] == null ? 0 : Double.valueOf(o[12].toString())) - (o[13] == null ? 0 : Double.valueOf(o[13].toString())));  
         map.put("billStatus", o[14]);  
         map.put("payTime", o[18] == null ? "" : o[18]);  
         int overDueDay = 0;  
         if ("0".equals(o[14].toString())) {  
            overDueDay = DateUtil.differentDays(o[5].toString(), DateUtil.getTime1(new Date()));  
         }  
         map.put("overDueDay", overDueDay);  
         map.put("totalContractNo", o[17] == null ? "" : o[17]);  
         map.put("houseId", o[19]);  
         map.put("costType", o[20]);  
         if (StringUtils.isNotBlank(type) && "1".equals(type) && "04".equals(o[11].toString())) {  
            map.put("totalFlag", "1");  
         } else {  
            map.put("totalFlag", o[21]);  
         }  
         map.put("billNo", o[15]);  
         map.put("contractNo", o[16]);  
         map.put("voucherNo", o[25] == null ? "" : o[25]);  
         map.put("voucherUrl", o[26] == null ? "" : o[26]);  
         map.put("smsAdv", o[29] == null ? "" : o[29]);  
         map.put("smsCause", o[30] == null ? "" : o[30]);  
         map.put("cashier", o[31] == null ? "0" : o[31]);  
         map.put("batchNo", o[32] == null ? "0" : o[32]);  
         map.put("contractStartDate", o[33] == null ? "" : o[33]);  
         map.put("contractEndDate", o[34] == null ? "" : o[34]);  
         map.put("location", o[35] == null ? "" : o[35]);  
         map.put("remark", o[36] == null ? "" : o[36]);  
         map.put("signDate", o[37] == null ? "" : o[37]);  
         map.put("master", o[38] == null ? "" : o[38]);  
         map.put("approverFlag", o[39] == null ? "0" : o[39]);  
         map.put("rentCenter", o[40] == null ? "" : o[40]);  
         map.put("subsidyMoney", o[41] == null ? "0" : o[41]);  
         map.put("community", o[42] == null ? "0" : o[42]);  
         listJson.add(map);  
      }  
   }  
   Map<String, Object> count = new HashMap<>();  
   count.put("total", count1);  
   return Response.ok(RestResult.ok().data(new MessagePackNew<>((long) pageIndex, pageTotal, count, listJson)))  
         .header("Access-Control-Allow-Origin", "*").build();  
}
```

## 抽出map构造为独立方法

```java
private List<Map<String, Object>> getBillListReturnMaps(String type, List<Object[]> bills) {

	List<Map<String, Object>> listJson = new ArrayList<Map<String, Object>>();  
	for (int i = 0; i < bills.size(); i++) {  
	   List<Object[]> li = new ArrayList<Object[]>();  
	   li.add(bills.get(i));  
	   for (Object[] o : li) {  
	      Map<String, Object> map = new HashMap<String, Object>();  
	      map.put("id", i);  
	      map.put("projectName", o[0]);  
	      map.put("houseNo", o[1]);
	      ......

}
```

抽出map的构造方法为独立方法，现在查询和下载都可以复用

## 修改下载接口的for循环

将下载的for循环进行适当修改

原：

```java
String[][] content = new String[listJson.size()][title.length];  
for (int i = 0; i < listJson.size(); i++) {  
   Map<String, Object> obj = listJson.get(i);  
   content[i][0] = String.valueOf(obj.get("houseNo"));  
   content[i][1] = String.valueOf(obj.get("projectName"));  
   content[i][2] = "04".equals(String.valueOf(obj.get("contractAttribute"))) ? "是" : "否";  
   content[i][3] = String.valueOf(obj.get("payer"));  
   content[i][4] = String.valueOf(obj.get("payerTel"));  
   content[i][5] = String.valueOf(obj.get("billStartTime")) + "-" + String.valueOf(obj.get("billEndTime"));  
   content[i][6] = String.valueOf(obj.get("name"));  
   content[i][7] = String.valueOf(obj.get("tenantName"));  
   content[i][8] = String.valueOf(obj.get("jiaofeiType"));  
   content[i][9] = "04".equals(String.valueOf(obj.get("contractAttribute"))) ? "包租" : "02".equals(String.valueOf(obj.get("contractAttribute"))) ? "单位" : "个人";  
   content[i][10] = String.valueOf(obj.get("billAmount"));  
   content[i][11] = String.valueOf(obj.get("unBillPaidAmount"));  
   content[i][12] = String.valueOf(obj.get("collectionPeriod"));  
   content[i][13] = "0".equals(String.valueOf(obj.get("billStatus"))) ? "未支付" : "已支付";  
   content[i][14] = String.valueOf(obj.get("voucherNo"));  
   content[i][15] = String.valueOf(obj.get("overDueDay"));  
}
```

结果：

```java
List<String[]> contentList = new ArrayList<>();  
   listJson.forEach(obj -> {  
      String[] content = {  
            String.valueOf(obj.get("houseNo")),  
            String.valueOf(obj.get("projectName")),  
            "04".equals(String.valueOf(obj.get("contractAttribute"))) ? "是" : "否",  
            String.valueOf(obj.get("master")),  
            String.valueOf(obj.get("tenantName")),  
            String.valueOf(obj.get("payer")),  
            String.valueOf(obj.get("payerTel")),  
            String.valueOf(obj.get("billStartTime")) + " - " + String.valueOf(obj.get("billEndTime")),  
            String.valueOf(obj.get("name")),  
            String.valueOf(obj.get("jiaofeiType")),  
            StatusUtil.getContractAttributeString(String.valueOf(obj.get("contractAttribute"))),  
            String.valueOf(obj.get("billAmount")),  
            String.valueOf(obj.get("unBillPaidAmount")),  
            String.valueOf(obj.get("subsidyMoney")),// 免租金额  
            String.valueOf(obj.get("collectionPeriod")),// 收账日期  
            String.valueOf(obj.get("payTime")),// 实收日期  
            "0".equals(String.valueOf(obj.get("billStatus"))) ? "未支付" : "已支付",  
            String.valueOf(obj.get("smsAdv")),// 短信  
            DateUtil.fomatDate3(String.valueOf(obj.get("signDate"))),// 签约日期  
            DateUtil.fomatDate3(String.valueOf(obj.get("contractStartDate"))) + " - " + DateUtil.fomatDate3(String.valueOf(obj.get("contractEndDate"))),  
            String.valueOf(obj.get("remark")),// 备注  
            String.valueOf(obj.get("voucherNo")),// 凭证号  
            String.valueOf(obj.get("overDueDay")),  
            String.valueOf(obj.get("smsCause")) // 失败原因  
      };  
      contentList.add(content);  
   });  
```

修改为`foreach`循环，避免了使用复杂的数组下标

## 抽出对于入参的处理

原：

```java
String[] operators = null;  
if (StringUtils.isNotBlank(operator)) {  
    operators = operator.split(",");  
}  
String[] projectNames = null;  
if (StringUtils.isNotBlank(projectName)) {  
    projectNames = projectName.split(",");  
}  
String[] HouseStatus = null;  
if (StringUtils.isNotEmpty(houseStatus)) {  
    HouseStatus = houseStatus.split(",");  
}  
String[] buildingNos = null;  
if (StringUtils.isNotBlank(buildingNo)) {  
    buildingNos = buildingNo.split(",");  
}  
String[] roomTypes = null;  
if (StringUtils.isNotBlank(roomType)) {  
    roomTypes = roomType.split(",");  
}
```

现：

```java
String[] operators = splitArrayStrings(operator);  
String[] projectNames = splitArrayStrings(projectName);  
String[] buildingNos = splitArrayStrings(buildingNo);  
String[] roomTypes = splitArrayStrings(roomType);

private static String[] splitArrayStrings(String str) {  
    if (StringUtils.isNotBlank(str)) {  
        return str.split(",");  
    }  
    return null;  
}
```


## 抽离需要对null处理的不同逻辑

```java
private <T> String nullObjBlankHandler(T t) {  
    if (t == null) return "";  
    return String.valueOf(t);  
}  
  
private <T> String nullObjZeroHandler(T t) {  
    if (t == null) return "0";  
    return String.valueOf(t);  
}
```

## 进一步修改map构造方法

有特殊处理的地方单独`put`，其他按照数组下标遍历，注意部分参与未使用则跳过

原来：

```java
   List<Map<String, Object>> listJson = new ArrayList<Map<String, Object>>();  
   for (int i = 0; i < bills.size(); i++) {  
      List<Object[]> li = new ArrayList<Object[]>();  
      li.add(bills.get(i));  
      for (Object[] o : li) {  
         Map<String, Object> map = new HashMap<String, Object>();  
         map.put("id", i);  
         map.put("projectName", o[0]);  
         map.put("houseNo", o[1]);  
         long houseCount = 0;  
         if (o[11] != null && "04".equals(o[11].toString())) {  
            if (o[19] != null) {  
               String[] split = o[19].toString().split(",");  
               houseCount = split.length;  
            }  
         }  
         map.put("houseCount", houseCount);  
         map.put("payer", o[27] == null ? (o[2] == null ? "" : o[2]) : o[27]);  
         map.put("payerTel", o[3] == null ? (o[28] == null ? "" : o[28]) : o[3]);  
         map.put("collectionPeriod", o[4]);  
         map.put("billStartTime", o[5]);  
         map.put("billEndTime", o[6]);  
         map.put("name", o[7]);  
         map.put("tenantName", o[8]);  
         map.put("jiaofeiType", o[9] == null ? "" : o[9]);  
         map.put("contractAttribute", o[11]);  
         map.put("billAmount", o[12]);  
         map.put("billPaidAmount", o[13]);  
         map.put("unBillPaidAmount", Math.abs(o[12] == null ? 0 : Double.valueOf(o[12].toString())) - (o[13] == null ? 0 : Double.valueOf(o[13].toString())));  
         map.put("billStatus", o[14]);  
         map.put("payTime", o[18] == null ? "" : o[18]);  
         int overDueDay = 0;  
         if ("0".equals(o[14].toString())) {  
            overDueDay = DateUtil.differentDays(o[5].toString(), DateUtil.getTime1(new Date()));  
         }  
         map.put("overDueDay", overDueDay);  
         map.put("totalContractNo", o[17] == null ? "" : o[17]);  
         map.put("houseId", o[19]);  
         map.put("costType", o[20]);  
         if (StringUtils.isNotBlank(type) && "1".equals(type) && "04".equals(o[11].toString())) {  
            map.put("totalFlag", "1");  
         } else {  
            map.put("totalFlag", o[21]);  
         }  
         map.put("billNo", o[15]);  
         map.put("contractNo", o[16]);  
         map.put("voucherNo", o[25] == null ? "" : o[25]);  
         map.put("voucherUrl", o[26] == null ? "" : o[26]);  
         map.put("smsAdv", o[29] == null ? "" : o[29]);  
         map.put("smsCause", o[30] == null ? "" : o[30]);  
         map.put("cashier", o[31] == null ? "0" : o[31]);  
         map.put("batchNo", o[32] == null ? "0" : o[32]);  
         map.put("contractStartDate", o[33] == null ? "" : o[33]);  
         map.put("contractEndDate", o[34] == null ? "" : o[34]);  
         map.put("location", o[35] == null ? "" : o[35]);  
         map.put("remark", o[36] == null ? "" : o[36]);  
         map.put("signDate", o[37] == null ? "" : o[37]);  
         map.put("master", o[38] == null ? "" : o[38]);  
         map.put("approverFlag", o[39] == null ? "0" : o[39]);  
         map.put("rentCenter", o[40] == null ? "" : o[40]);  
         map.put("subsidyMoney", o[41] == null ? "0" : o[41]);  
         map.put("community", o[42] == null ? "0" : o[42]);  
         listJson.add(map);  
      }  
   }  
```

现：

```java

String[] keys = {"projectName", "buildingNo", "houseStatus", "contractAttribute", "totalFlag",  
        "houseId", "houseNo", "roomType", "contractNo", "contractStartDate",  
        "contractEndDate", "tenantName", "contactMobile", "cardNo", "houseMate",  
        "jiaofeiType", "day", "rentPaidTo", "retractDate", "tenantNumber", "signDate",  
        "emergencyContact", "emergencyContactNumber", "isLive", "contractStatus", "totalContractNo",  
        "source", "firstContractStartDate", "litigation", "uploadStatus", "tenantName", "payDiff", "liveTime",  
        "liveEndDate", "actualRent", "contractSendStatus", "contractSender", "contractSendTime",  
        "contractReceiver", "contractReceiveTime", "contractUploader", "contractUploadTime", "contractAddition",  
        "rentFree"  
};  
List<String> zeroKeys = Arrays.asList("isLive", "rentFree");  
  
  
for (Object[] o : contractList) {  
    Map<String, String> map = new HashMap<>();  
    for (int j = 0; j < o.length; j++) {  
        map.put("id", String.valueOf(j + 1));  
        if (zeroKeys.contains(keys[j])) {  
            map.put(keys[j], nullObjZeroHandler(o[j]));  
        } else {  
            map.put(keys[j], nullObjBlankHandler(o[j]));  
        }  
    }  
  
    map.put("month", String.valueOf(DateUtil.getMonthDiff(map.get("contractStartDate"), map.get("contractEndDate"))));  
    map.put("day", calDayValue(o[16], o[31]));
  
}

```

## 修改下载的构造方法

因为下载的字段与查询的字段有对应关系，所以我们准备两个映射的数据进行对应

修改后：

```java
List<Map<String, Object>> listJson = getHouseListResultMaps(houseStatus, houses);  
    String[] title = {"项目名称", "房号", "房屋ID", "楼号", "层号", "室号", "房型", "朝向", "建筑面积", "单价", "坐落位置", "租金",  
"市场租金", "房源类型", "房源来源", "户型配置", "房源状态", "经租中心","锁定操作人","锁定时间","锁定原因"};  
    String[] names = {"projectName", "houseNo", "houseId", "buildingNo", "buildingFloor", "roomNo", "roomType", "towards", "area", "unitPrice", "location", "monthlyRent",  
            "marketRent", "houseType", "houseSource", "roomType", "houseStatus", "operator", "freezeOperator", "freezeRecordTime", "freezeRemark"};  
    // 将null返回""  
    List<String> blankStrKeys = Arrays.asList("marketRent", "freezeOperator", "freezeRecordTime", "freezeRemark");  
  
    List<String[]> contentList = new ArrayList<>();  
    listJson.forEach(obj -> {  
        String[] content = new String[names.length];  
        for (int i = 0; i < content.length; i++) {  
            content[i] =String.valueOf( obj.get(names[i]));  
            if (blankStrKeys.contains(names[i])) {  
                content[i] = nullObjStringHandler (obj.get(names[i]));  
            }  
        }  
        contentList.add(content);  
    });
```


## 结果总览

经过了多轮方法抽离与算法重构，最终结果如下：

```java
public RestResult<Object> getContractList(String operator, String projectName, String contractStatus,  
                                          String houseStatus, String contractAttribute, String dueMonth, String buildingNo,  
                                          String roomType, String community, String startTime,  
                                          String endTime, String master, String tenantName,  
                                          String contractNo, String houseNo, String type,  
                                          String litigation, String uploadStatus,  
                                          String signSource, String isLive, String cardNo,  
                                          String liveEndDate, String isAttachment,  
                                          Integer pageIndex, Integer pageSize) {  
    String[] operators = splitArrayStrings(operator);  
    String[] projectNames = splitArrayStrings(projectName);  
    String[] buildingNos = splitArrayStrings(buildingNo);  
    String[] roomTypes = splitArrayStrings(roomType);  
    String[] houseStatusSqlParam = splitArrayStrings(houseStatus);  
    String[] contractStatusSqlParam = null;  
    if (StringUtils.isNotEmpty(contractStatus)) {  
        String all = getContractStatusString(contractStatus);  
        contractStatusSqlParam = all.split(",");  
    }  
  
    String dueTime = null;//到期时间  
    String expireTime = null;//逾期时间  
    if (StringUtils.isNotEmpty(dueMonth)) {  
        if ("0".equals(dueMonth)) {  
            expireTime = DateUtil.getTime1(new Date());  
        } else {  
            dueTime = DateUtil.addMonth(DateUtil.getTime1(new Date()), Integer.parseInt(dueMonth));  
        }  
    }  
  
    List<Object[]> contractList = contractListSQL(operators, projectNames, contractStatusSqlParam, contractAttribute, dueTime, expireTime,  
            buildingNos, roomTypes, community, startTime, endTime, contractNo, houseNo, tenantName, master, type, signSource, isLive, houseStatusSqlParam, litigation, uploadStatus, cardNo, liveEndDate, isAttachment, pageIndex, pageSize);  
    List<Map<String, String>> listJson = getContractListMaps(contractList, false);  
  
  
    List<Object[]> contractCount = contractCountSQL(operators, projectNames, contractStatusSqlParam, contractAttribute, dueTime, expireTime,  
            buildingNos, roomTypes, community, startTime, endTime, contractNo, houseNo, tenantName, master, type, signSource, isLive, houseStatusSqlParam, litigation, uploadStatus, cardNo, liveEndDate, isAttachment);  
    long count1 = (long) (contractCount.get(0) == null ? 0 : Double.parseDouble(String.valueOf(contractCount.get(0))));  
    long pageTotal = (long) Math.ceil((double) count1 / pageSize);  
    Map<String, Object> count = new HashMap<>();  
    count.put("total", count1);  
  
    return RestResult.ok().data(new MessagePackNew<>((long) pageIndex, pageTotal, count, listJson));  
}
```

这是一个合同查询接口，从原来的200行->30行主函数，实现了极大的接口重构。当然最后还需要抽离到service层。
