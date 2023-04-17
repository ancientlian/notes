原：

```java
for (int i = 0; i < bills.size(); i++) {
	List<Object[]> li = new ArrayList<Object[]>();
	li.add(bills.get(i));
	for (Object[] o : li) {
		Map<String, Object> map = new HashMap<String, Object>();
		map.put("totalSettlementNo", o[0]);
		map.put("contractNo", o[1]);
		map.put("settlementNo", o[2]);
		map.put("master", o[3]);
		map.put("retractTime", o[4]);
		map.put("contractStatus", o[5]);
		map.put("actualRent", o[6]);
		map.put("returnRent", o[7]);
		map.put("projectName", o[8]);
		map.put("houseNo", (o[9] == null ? "" : o[9]));
		map.put("contractAttribute", (o[10] == null ? "" : o[10]));
		map.put("contactName", (o[11] == null ? "" : o[11]));
		map.put("tenantName", (o[3] == null ? "" : o[3]));
		map.put("status", (o[13] == null ? "" : o[13]));
		map.put("retractType", (o[14] == null ? "" : o[14]));
		map.put("id", (o[15] == null ? "" : o[15]));
		map.put("totalContractNo", (o[16] == null ? "" : o[16]));
		map.put("houseId", (o[17] == null ? "" : o[17]));
		map.put("actualRefundTime", (o[18] == null ? "" : o[18]));
		map.put("tenantId", (o[19] == null ? "" : o[19]));
		listJson.add(map);
	}
}
```

现：

```java
listJson = bills.stream().map(bill -> {
    Map<String, Object> map = new HashMap<>();
    map.put("totalSettlementNo", bill[0]);
    map.put("contractNo", bill[1]);
    map.put("settlementNo", bill[2]);
    map.put("master", bill[3]);
    map.put("retractTime", bill[4]);
    map.put("contractStatus", bill[5]);
    map.put("actualRent", bill[6]);
    map.put("returnRent", bill[7]);
    map.put("projectName", bill[8]);
    map.put("houseNo", (bill[9] == null ? "" : bill[9]));
    map.put("contractAttribute", (bill[10] == null ? "" : bill[10]));
    map.put("contactName", (bill[11] == null ? "" : bill[11]));
    map.put("tenantName", (bill[3] == null ? "" : bill[3]));
    map.put("status", (bill[13] == null ? "" : bill[13]));
    map.put("retractType", (bill[14] == null ? "" : bill[14]));
    map.put("id", (bill[15] == null ? "" : bill[15]));
    map.put("totalContractNo", (bill[16] == null ? "" : bill[16]));
    map.put("houseId", (bill[17] == null ? "" : bill[17]));
    map.put("actualRefundTime", (bill[18] == null ? "" : bill[18]));
    map.put("tenantId", (bill[19] == null ? "" : bill[19]));
    return map;
}).collect(Collectors.toList());

```
