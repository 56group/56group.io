---
title:  常用代码
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