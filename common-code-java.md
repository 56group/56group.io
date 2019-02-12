---
title:  Java常用代码
date: 2018-10-11 15:39:00
tags: [常用, JAVA, CODE]
categories:  常用
---
##### Spring JdbcTemplate批量操作

```
String sql = "INSERT INTO baoxiu_placeroom (roomId, roomName, buildingId, roomNumber) VALUES (?, ?, ?, ?)";

jdbcTemplate.batchUpdate(sql, new BatchPreparedStatementSetter() {
    @Override
  public void setValues(PreparedStatement preparedStatement, int i) throws SQLException {
        String roomId = rooms.get(i).getRoomId();
        String roomName = rooms.get(i).getRoomName();
        String buildingId = rooms.get(i).getBuildingId();
        String roomNumber = rooms.get(i).getRoomNumber();

        preparedStatement.setString(1, roomId);
        preparedStatement.setString(2, roomName);
        preparedStatement.setString(3, buildingId);
        preparedStatement.setString(4, roomNumber);
    }

    @Override
  public int getBatchSize() {
        return rooms.size();
    }
});

return true;
```
##### JdbcTemplate IN参数处理

```
public boolean update4RenameEquipment(Equipment equipment) {
    List<String> names = Arrays.asList(equipment.getEquipmentNames().split(","));

    NamedParameterJdbcTemplate tmpJdbcTemplate = new NamedParameterJdbcTemplate(jdbcTemplate.getDataSource());

    Map<String, Object> params = new HashMap<>();

    params.put("newEquipmentName", equipment.getNewEquipmentName());
    params.put("equipmentNames", names);

    String sql = "UPDATE baoxiu_equipment SET equipmentName = :newEquipmentName WHERE equipmentName IN (:equipmentNames)";

    return tmpJdbcTemplate.update(sql, params) >= 0;
}
```

##### MyBatis批量更新代码

```
public String updateBatchDeleteUsers(Map map) {
    List<String> userIds = (List<String>) map.get("userIds");

    StringBuilder builder = new StringBuilder();

    builder.append("UPDATE system_user SET deleteFlag = 1 WHERE userId IN (");

    StringBuilder valueFormatBuilder = new StringBuilder();
    valueFormatBuilder.append("#'{'userIds[{0}]}");

    MessageFormat mf = new MessageFormat(valueFormatBuilder.toString());
    for (int i = 0; i < userIds.size(); i++) {

        builder.append(mf.format(new Object[]{i}));

        if (i < userIds.size() - 1) {
            builder.append(",");
        }
    }

    builder.append(")");

    return builder.toString();
}
```
##### MyBatis批量更新xml

```
<update id="updateListNumberBatch" parameterType="com.zhulin.bus.bean.History">
    UPDATE baoxiu_liststatetime
    <trim prefix="SET" suffixOverrides=",">
        <trim prefix="listNumber =case" suffix="end,">
            <foreach collection="list" item="item" index="index">
                WHEN liststatetimeid=#{item.liststatetimeid} THEN #{item.newListNumber}
            </foreach>
        </trim>
    </trim>
    WHERE liststatetimeid IN
    <foreach collection="list" index="index" item="item" separator="," open="(" close=")">
        #{item.liststatetimeid}
    </foreach>
</update>
```