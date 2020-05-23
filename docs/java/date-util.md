# 日期工具类

---

一个低级工具类


```java
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;

@SuppressWarnings("SpellCheckingInspection")
public class DateUtil {

  private static final String YYYYMMDD = "yyyy-MM-dd";

  private static final String YYYYMMDD_ZH = "yyyy年MM月dd日";

  private static final String YYYYMMDDHH = "yyyy-MM-dd HH";

  private static final String YYYYMMDDHHMMSS = "yyyy-MM-dd HH:mm:ss";

  private static final int FIRST_DAY_OF_WEEK = Calendar.MONDAY; // 中国周一是一周的第一天

  private static final long oneDayTime = 86400000;

  /**
   * str2Date 将指定形式的时间字符串转换成时间
   *
   * @param strDate 时间的字符串
   * @param pattern 指定的时间形式
   * @return 返回Date
   */
  private static Date str2Date(String strDate, String pattern) {
    Date date = null;
    try {
      if (pattern == null) {
        pattern = YYYYMMDD;
      }
      SimpleDateFormat format = new SimpleDateFormat(pattern);
      date = format.parse(strDate);
    } catch (Exception e) {
      System.out.println("str2Date error:" + e);
    }
    return date;
  }

  /**
   * 将日期按照置顶格式转换成字符串
   *
   * @param date    时间
   * @param pattern 时间格式
   * @return 格式化后的时间的字符串
   */
  private static String date2String(Date date, String pattern) {
    String strDate = null;
    try {
      if (pattern == null) {
        pattern = YYYYMMDD;
      }
      SimpleDateFormat format = new SimpleDateFormat(pattern);
      strDate = format.format(date);
    } catch (Exception e) {
      System.out.println("date2String error:" + e);
    }
    return strDate;
  }

  /**
   * 取得日期：年
   *
   * @param date 时间
   * @return 年份
   */
  public static int getYear(Date date) {
    Calendar c = Calendar.getInstance();
    c.setTime(date);
    return c.get(Calendar.YEAR);
  }

  /**
   * 取得日期：月
   *
   * @param date 时间
   * @return 月份
   */
  public static int getMonth(Date date) {
    Calendar c = Calendar.getInstance();
    c.setTime(date);
    int month = c.get(Calendar.MONTH);
    return month + 1;
  }

  /**
   * 取得日期：日
   *
   * @param date 时间
   * @return 日
   */
  public static int getDay(Date date) {
    Calendar c = Calendar.getInstance();
    c.setTime(date);
    return c.get(Calendar.DAY_OF_MONTH);
  }

  /**
   * 取得当天是周几
   *
   * @param date 时间
   * @return 星期
   */
  public static int getDayOfWeek(Date date) {
    Calendar c = Calendar.getInstance();
    c.setTime(date);
    int dayOfWeek = c.get(Calendar.DAY_OF_WEEK);
    return dayOfWeek - 1;
  }

  /**
   * 取得一年的第几周
   *
   * @param date 时间
   * @return 该时间是第多少周
   */
  public static int getWeekOfYear(Date date) {
    Calendar c = Calendar.getInstance();
    c.setTime(date);
    return c.get(Calendar.WEEK_OF_YEAR);
  }

  /**
   * 获取指定日期所处周的周一日期和周日日期
   *
   * @param date    时间
   * @param pattern 时间格式
   * @return 以" - " 连接的字符串
   */
  public static String getBeginAndEndDateOfWeek(Date date, String pattern) {
    Date monday = getMondayOfWeek(date);
    Date sunday = getSundayOfWeek(date);
    return date2String(monday, pattern) + " - "
        + date2String(sunday, pattern);
  }

  /**
   * 根据日期取得对应周周一日期
   *
   * @param date 时间
   * @return 周一的日期
   */
  private static Date getMondayOfWeek(Date date) {
    Calendar monday = Calendar.getInstance();
    monday.setTime(date);
    monday.setFirstDayOfWeek(FIRST_DAY_OF_WEEK);
    monday.set(Calendar.DAY_OF_WEEK, Calendar.MONDAY);
    return monday.getTime();
  }

  /**
   * 根据日期取得对应周周日日期
   *
   * @param date 时间
   * @return 周日的日期
   */
  private static Date getSundayOfWeek(Date date) {
    Calendar sunday = Calendar.getInstance();
    sunday.setTime(date);
    sunday.setFirstDayOfWeek(FIRST_DAY_OF_WEEK);
    sunday.set(Calendar.DAY_OF_WEEK, Calendar.SUNDAY);
    return sunday.getTime();
  }

  /**
   * 取得月的剩余天数
   *
   * @param date 时间
   * @return 本月剩余的天数
   */
  public static int getRemainDayOfMonth(Date date) {
    int dayOfMonth = getDayOfMonth(date);
    int passDayOfMonth = getPassDayOfMonth(date);
    return dayOfMonth - passDayOfMonth;
  }

  /**
   * 取得月已经过的天数
   *
   * @param date 时间
   * @return 本月已经过的天数
   */
  private static int getPassDayOfMonth(Date date) {
    Calendar c = Calendar.getInstance();
    c.setTime(date);
    return c.get(Calendar.DAY_OF_MONTH);
  }

  /**
   * 取得月天数
   *
   * @param date 时间
   * @return 本月的第多少天
   */
  private static int getDayOfMonth(Date date) {
    Calendar c = Calendar.getInstance();
    c.setTime(date);
    return c.getActualMaximum(Calendar.DAY_OF_MONTH);
  }

  /**
   * 取得月第一天
   *
   * @param date 时间
   * @return 本月的第一天的日期
   */
  private static Date getFirstDateOfMonth(Date date) {
    Calendar c = Calendar.getInstance();
    c.setTime(date);
    c.set(Calendar.DAY_OF_MONTH, c.getActualMinimum(Calendar.DAY_OF_MONTH));
    return c.getTime();
  }

  /**
   * 取得月最后一天
   *
   * @param date 时间
   * @return 本月的最后一天的日期
   */
  private static Date getLastDateOfMonth(Date date) {
    Calendar c = Calendar.getInstance();
    c.setTime(date);
    c.set(Calendar.DAY_OF_MONTH, c.getActualMaximum(Calendar.DAY_OF_MONTH));
    return c.getTime();
  }

  /**
   * 取得季度第一天
   *
   * @param date 时间
   * @return 本季度的第一天的日期
   */
  public static Date getFirstDateOfQuarter(Date date) {
    return getFirstDateOfMonth(getSeasonQuarter(date)[0]);
  }

  /**
   * 取得季度最后一天
   *
   * @param date 时间
   * @return 本季度的最后一天的日期
   */
  public static Date getLastDateOfQuarter(Date date) {
    return getLastDateOfMonth(getSeasonQuarter(date)[2]);
  }

  /**
   * 取得季度天数
   *
   * @param date 时间
   * @return 本季度的第多少天
   */
  private static int getDayOfQuarter(Date date) {
    int day = 0;
    Date[] seasonDates = getSeasonQuarter(date);
    for (Date date2 : seasonDates) {
      day += getDayOfMonth(date2);
    }
    return day;
  }

  /**
   * 取得季度剩余天数
   *
   * @param date 时间
   * @return 本季度剩余的天数
   */
  public static int getRemainDayOfQuarter(Date date) {
    return getDayOfQuarter(date) - getPassDayOfQuarter(date);
  }

  /**
   * 取得季度已过天数
   *
   * @param date 时间
   * @return 本季度已过的天数
   */
  private static int getPassDayOfQuarter(Date date) {
    int day = 0;

    Date[] seasonDates = getSeasonQuarter(date);

    Calendar c = Calendar.getInstance();
    c.setTime(date);
    int month = c.get(Calendar.MONTH);

    if (month == Calendar.JANUARY || month == Calendar.APRIL
        || month == Calendar.JULY || month == Calendar.OCTOBER) { // 季度第一个月
      day = getPassDayOfMonth(seasonDates[0]);
    } else if (month == Calendar.FEBRUARY || month == Calendar.MAY
        || month == Calendar.AUGUST || month == Calendar.NOVEMBER) { // 季度第二个月
      day = getDayOfMonth(seasonDates[0])
          + getPassDayOfMonth(seasonDates[1]);
    } else if (month == Calendar.MARCH || month == Calendar.JUNE
        || month == Calendar.SEPTEMBER || month == Calendar.DECEMBER) { // 季度第三个月
      day = getDayOfMonth(seasonDates[0]) + getDayOfMonth(seasonDates[1])
          + getPassDayOfMonth(seasonDates[2]);
    }
    return day;
  }

  /**
   * 取得季度月
   * 输入2018-10-24，返回[2018-10-24, 2018-11-24, 2018-12-24]
   *
   * @param date 时间
   * @return 取季度月
   */
  private static Date[] getSeasonQuarter(Date date) {
    Date[] quarter = new Date[3];

    Calendar c = Calendar.getInstance();
    c.setTime(date);

    int nSeason = getQuarter(date);
    if (nSeason == 1) { // 第一季度
      c.set(Calendar.MONTH, Calendar.JANUARY);
      quarter[0] = c.getTime();
      c.set(Calendar.MONTH, Calendar.FEBRUARY);
      quarter[1] = c.getTime();
      c.set(Calendar.MONTH, Calendar.MARCH);
      quarter[2] = c.getTime();
    } else if (nSeason == 2) { // 第二季度
      c.set(Calendar.MONTH, Calendar.APRIL);
      quarter[0] = c.getTime();
      c.set(Calendar.MONTH, Calendar.MAY);
      quarter[1] = c.getTime();
      c.set(Calendar.MONTH, Calendar.JUNE);
      quarter[2] = c.getTime();
    } else if (nSeason == 3) { // 第三季度
      c.set(Calendar.MONTH, Calendar.JULY);
      quarter[0] = c.getTime();
      c.set(Calendar.MONTH, Calendar.AUGUST);
      quarter[1] = c.getTime();
      c.set(Calendar.MONTH, Calendar.SEPTEMBER);
      quarter[2] = c.getTime();
    } else if (nSeason == 4) { // 第四季度
      c.set(Calendar.MONTH, Calendar.OCTOBER);
      quarter[0] = c.getTime();
      c.set(Calendar.MONTH, Calendar.NOVEMBER);
      quarter[1] = c.getTime();
      c.set(Calendar.MONTH, Calendar.DECEMBER);
      quarter[2] = c.getTime();
    }
    return quarter;
  }

  /**
   * 1 第一季度 2 第二季度 3 第三季度 4 第四季度
   *
   * @param date 时间
   * @return 取当前时间所在的季度
   */
  private static int getQuarter(Date date) {

    int quarter = 0;

    Calendar c = Calendar.getInstance();
    c.setTime(date);
    int month = c.get(Calendar.MONTH);
    switch (month) {
      case Calendar.JANUARY:
      case Calendar.FEBRUARY:
      case Calendar.MARCH:
        quarter = 1;
        break;
      case Calendar.APRIL:
      case Calendar.MAY:
      case Calendar.JUNE:
        quarter = 2;
        break;
      case Calendar.JULY:
      case Calendar.AUGUST:
      case Calendar.SEPTEMBER:
        quarter = 3;
        break;
      case Calendar.OCTOBER:
      case Calendar.NOVEMBER:
      case Calendar.DECEMBER:
        quarter = 4;
        break;
      default:
        break;
    }
    return quarter;
  }

  /**
   * 判断时间是否在今天以后
   *
   * @param time 指定时间，格式2018-01-01 00:00:00
   * @return true：以前或者现在  false：未来
   */
  public static boolean isExistTime(String time) {
    SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    Date date = null;
    try {
      date = sdf.parse(time);
    } catch (ParseException e) {
      e.printStackTrace();
    }
    if (date != null) {
      return !date.after(new Date());
    }
    return false;
  }

  /**
   * 获取指定日期后第N天日期
   *
   * @param oldDate 指定日期
   * @param dayNum  N天后
   * @return 新的日期
   */
  public static Date addDay(Date oldDate, int dayNum) {
    long oldTime = oldDate.getTime();
    long addTime = oneDayTime * dayNum;
    long newTime = oldTime + addTime;
    return new Date(newTime);
  }

  /**
   * 获取指定月份的每一天
   * @param yearParam 年
   * @param monthParam 月
   * @return 每天组成的字符串集合
   */
  public static List<String> getDayByMonth(int yearParam, int monthParam) {
    List<String> list = new ArrayList<>();
    Calendar aCalendar = Calendar.getInstance(Locale.CHINA);
    aCalendar.set(yearParam, monthParam - 1, 1);
    int year = aCalendar.get(Calendar.YEAR);//年份
    int month = aCalendar.get(Calendar.MONTH) + 1;//月份
    int day = aCalendar.getActualMaximum(Calendar.DATE);
    for (int i = 1; i <= day; i++) {
      String aDate = null;
      if (month < 10 && i < 10) {
        aDate = String.valueOf(year) + "-0" + month + "-0" + i;
      }
      if (month < 10 && i >= 10) {
        aDate = String.valueOf(year) + "-0" + month + "-" + i;
      }
      if (month >= 10 && i < 10) {
        aDate = String.valueOf(year) + "-" + month + "-0" + i;
      }
      if (month >= 10 && i >= 10) {
        aDate = String.valueOf(year) + "-" + month + "-" + i;
      }

      list.add(aDate);
    }
    return list;
  }

  /**
   * 按类型拆分时间段
   *
   * @param startTime 开始时间，格式YYYY-MM-dd HH:mm:ss
   * @param endTime   结束时间，格式YYYY-MM-dd HH:mm:ss
   * @param type      时间粒度，day或者DAY按天拆分。或者按小时
   * @return 时间集合
   */
  public static List<String> splitTime(String startTime, String endTime, String type) {
    int interval;
    if (type.equals("day") || type.equals("Day")) {
      interval = 60 * 24;
      if (startTime.length() != 10) {
        startTime = startTime.substring(0, 10) + " 00:00:00";
      }
    } else {
      interval = 60;
    }
    if (startTime.length() == 10) {
      startTime = startTime + " 00:00:00";
    }
    if (endTime.length() == 10) {
      endTime = endTime + " 23:59:59";
    }
    Date startDate = null;
    try {
      startDate = sdf.parse(startTime);
    } catch (ParseException e) {
      e.printStackTrace();
    }
    Date endDate = null;
    try {
      endDate = sdf.parse(endTime);
    } catch (ParseException e) {
      e.printStackTrace();
    }
    List<String> list = new ArrayList<>();
    while (startDate.getTime() <= endDate.getTime()) {
      list.add(ds.format(startDate));
      Calendar calendar = Calendar.getInstance();
      calendar.setTime(startDate);
      calendar.add(Calendar.MINUTE, interval);
      if (calendar.getTime().getTime() > endDate.getTime()) {
        if (!startDate.equals(endDate)) {
          list.add(ds.format(endDate));
        }
        startDate = calendar.getTime();
      } else {
        startDate = calendar.getTime();
      }
    }
    if (list.size() > 1) {
      if (list.get(list.size() - 1).equals(list.get(list.size() - 2))) {
        return list.subList(0, list.size() - 1);
      }
    }
    return list;
  }
}
```





----


使用JAVA8新增LocalDate类代替SimpleDateFormat


```java
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.time.Instant;
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.LocalTime;
import java.time.ZoneId;

public class DateUtil {

  private static final String FORMAT = "yyyy-MM-dd HH:mm:ss a";

  // 要在高并发环境下能有比较好的体验，可以使用ThreadLocal来限制SimpleDateFormat只能在线程内共享，这样就避免了多线程导致的线程安全问题。
  private static ThreadLocal<DateFormat> threadLocal = new ThreadLocal<DateFormat>() {
    @Override
    protected DateFormat initialValue() {
      return new SimpleDateFormat(FORMAT);
    }
  };

  private static String format(java.util.Date date) {
    return threadLocal.get().format(date);
  }

  /**
   * java.util.Date --> java.time.LocalDateTime
   */
  public static LocalDateTime uDateToLocalDateTime(java.util.Date date) {
    Instant instant = date.toInstant();
    ZoneId zone = ZoneId.systemDefault();
    return LocalDateTime.ofInstant(instant, zone);
  }

  /**
   * java.util.Date --> java.time.LocalDate
   */
  public static LocalDate uDateToLocalDate(java.util.Date date) {
    Instant instant = date.toInstant();
    ZoneId zone = ZoneId.systemDefault();
    LocalDateTime localDateTime = LocalDateTime.ofInstant(instant, zone);
    return localDateTime.toLocalDate();
  }

  /**
   * java.util.Date --> java.time.LocalTime
   */
  public static LocalTime uDateToLocalTime(java.util.Date date) {
    Instant instant = date.toInstant();
    ZoneId zone = ZoneId.systemDefault();
    LocalDateTime localDateTime = LocalDateTime.ofInstant(instant, zone);
    return localDateTime.toLocalTime();
  }

  /**
   * java.time.LocalDateTime --> java.util.Date
   */
  public static java.util.Date localDateTimeToUDate(LocalDateTime localDateTime) {
    ZoneId zone = ZoneId.systemDefault();
    Instant instant = localDateTime.atZone(zone).toInstant();
    return java.util.Date.from(instant);
  }

  /**
   * java.time.LocalDate --> java.util.Date
   */
  public static java.util.Date localDateToUDate(LocalDate localDate) {
    ZoneId zone = ZoneId.systemDefault();
    Instant instant = localDate.atStartOfDay().atZone(zone).toInstant();
    return java.util.Date.from(instant);
  }
}
```