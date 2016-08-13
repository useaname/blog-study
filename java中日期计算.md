---
date: 2016-08-10 16:09
status: public
tags: 'java, date'
title: java中日期计算
---

计算两个指定时间的相差天数：
```java
        Date aDate = new Date();
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        Date bDate = simpleDateFormat.parse("2016-08-5 12:12:12");


        Calendar w = Calendar.getInstance();
        w.setTime(aDate);

        Calendar t = Calendar.getInstance();
        t.setTime(bDate);
        t.set(Calendar.YEAR, w.get(Calendar.YEAR));
        int wDays = w.get(Calendar.DAY_OF_YEAR);
        int tDays = t.get(Calendar.DAY_OF_YEAR);
        System.out.println(wDays - tDays);
```
```java
    public static  void dateCal2(){
        // 设置cal的时间为2016-8-5
        Calendar cal = new GregorianCalendar(2016, Calendar.AUGUST, 5);
        System.out.println(cal.getTime());
        // 加上4个月 5天
        cal.add(Calendar.MONTH, 4);
        cal.add(Calendar.DAY_OF_MONTH, 5);
        cal.add(Calendar.HOUR, 7);


        // 加上25小时
        cal.add(Calendar.HOUR,25);
        // 设置指定的属性 的 值
        cal.set(Calendar.HOUR_OF_DAY, 20);

        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        String date = simpleDateFormat.format(cal.getTime());
        System.out.println(date);

        simpleDateFormat = new SimpleDateFormat("E d MMM yyyy hh:mm aaa ");
        date = simpleDateFormat.format(cal.getTime());
        System.out.println(date);

    }
```