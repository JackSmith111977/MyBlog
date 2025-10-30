---
title: "MyCalendarApp 开发文档"
date: 
tags:
    - Android
categories:
    - 项目开发
---

# MyCalendarApp

## 目录

- [MyCalendarApp](#mycalendarapp)
  - [目录](#目录)
  - [kizitonwose/CalendarView 框架的使用](#kizitonwosecalendarview-框架的使用)
    - [基本使用](#基本使用)
    - [周视图功能细分](#周视图功能细分)
      - [添加星期标题](#添加星期标题)
      - [添加日期选择功能](#添加日期选择功能)


## kizitonwose/CalendarView 框架的使用

[回到目录](#目录)

### 基本使用

[回到上一级](#kizitonwosecalendarview-框架的使用)

1. 添加依赖
在 gradle/libs.versions.toml 中：

~~~toml
[versions]
kizitonwose-calendar = "2.9.0"

[libraries]
kizitonwose-calendar = { module = "com.kizitonwose.calendar:view", version.ref = "kizitonwose-calendar" }
~~~

在 app/build.gradle.kts 中：

~~~kotlin
dependencies {
    implementation(libs.kizitonwose.calendar)
}
~~~

2. 创建日视图布局资源

在layout中创建日视图布局资源，例如 calendar_day_layout.xml 来为日历中的每一天创建一个布局

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:tools="http://schemas.android.com/tools">

    <TextView
        android:id="@+id/calendarDayText"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:gravity="center"
        tools:text="22"/>

</androidx.constraintlayout.widget.ConstraintLayout>
~~~

3. 创建 ViewContainer 类

为每个视图类型创建容器类来持有视图元素

月视图容器 (MonthDayViewContainer.kt)：

~~~kotlin
class MonthDayViewContainer(view: View) : ViewContainer(view) {
    val textView: TextView = view.findViewById(R.id.calendarDayText)
}
~~~

周视图容器 (DayViewContainer.kt)：

~~~kotlin
class DayViewContainer(view: View) : ViewContainer(view) {
    val textView: TextView = view.findViewById<TextView>(R.id.calendarDayText)
}
~~~

4. 创建 ViewBinder 类

创建绑定器类来控制如何显示日历中的每一天

月视图绑定器 (MonthDayViewBinder.kt)：

~~~kotlin
class MonthDayViewBinder : MonthDayBinder<MonthDayViewContainer> {
    override fun create(view: View) = MonthDayViewContainer(view)

    override fun bind(container: MonthDayViewContainer, data: CalendarDay) {
        container.textView.text = data.date.dayOfMonth.toString()
        
        // 根据日期是否属于当前月份设置不同的透明度
        if (data.position == DayPosition.MonthDate) {
            container.textView.alpha = 1f
        } else {
            container.textView.alpha = 0.3f
        }
    }
}
~~~

周视图绑定器 (WeekViewBinder.kt)：

~~~kotlin
class WeekViewBinder: WeekDayBinder<DayViewContainer> {
    override fun create(view: View): DayViewContainer = DayViewContainer(view)

    override fun bind(
        container: DayViewContainer,
        data: WeekDay
    ) {
        container.textView.text = data.date.dayOfMonth.toString()
    }
}
~~~

5. 创建 Fragment 布局

月视图布局 (fragment_month_view.xml)：

~~~xml
<com.kizitonwose.calendar.view.CalendarView
    android:id="@+id/month_calendar_view"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:cv_dayViewResource="@layout/calendar_day_layout"/>
~~~

周视图布局 (fragment_week_view.xml)：

~~~xml
<com.kizitonwose.calendar.view.WeekCalendarView
    android:id="@+id/week_calendar_view"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:cv_dayViewResource="@layout/calendar_day_layout"/>
~~~

6. 实现 Fragment 逻辑

月视图 Fragment (MonthViewFragment.kt)：

~~~kotlin
class MonthViewFragment : Fragment() {
    private var _binding: FragmentMonthViewBinding? = null
    private val binding get() = _binding!!

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        _binding = FragmentMonthViewBinding.inflate(inflater, container, false)
        return binding.root
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        setupMonthView()
    }

    private fun setupMonthView() {
        val currentDate = LocalDate.now()
        val currentMonth = YearMonth.now()
        val startMonth = currentMonth.minusMonths(100)
        val endMonth = currentMonth.plusMonths(100)
        val firstDayOfWeek = firstDayOfWeekFromLocale()

        binding.monthCalendarView.dayBinder = MonthDayViewBinder()
        binding.monthCalendarView.setup(startMonth, endMonth, firstDayOfWeek)
        binding.monthCalendarView.scrollToMonth(currentMonth)
    }

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}
~~~

周视图 Fragment (WeekViewFragment.kt)：

~~~kotlin
class WeekViewFragment: Fragment() {
    private var _binding: FragmentWeekViewBinding? = null
    private val binding get() = _binding!!

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        _binding = FragmentWeekViewBinding.inflate(inflater, container, false)
        return binding.root
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        setupWeekView()
    }

    private fun setupWeekView(){
        val currentDate = LocalDate.now()
        
        val weekCalendarView = binding.weekCalendarView
        weekCalendarView.dayBinder = WeekViewBinder()
        
        val currentMonth = YearMonth.now()
        val startMonth = currentMonth.minusMonths(100)
        val endMonth = currentMonth.plusMonths(100) 
        val firstDayOfWeek = firstDayOfWeekFromLocale()
        
        weekCalendarView.setup(startMonth.atDay(1), endMonth.atEndOfMonth(), firstDayOfWeek)
        weekCalendarView.scrollToWeek(currentDate)
    }

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}
~~~

至此，就完成了基于kizitonwose/CalendarView的日历视图的基本使用

### 周视图功能细分

[回到上一级](#kizitonwosecalendarview-框架的使用)

#### 添加星期标题

1. 在layout中创建calendar_day_title_text.xml定义**单个**星期标题的样式

~~~xml
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    android:layout_weight="1"
    android:layout_gravity="center"
    android:gravity="center"
    android:padding="8dp"
    android:textSize="12sp" />
~~~

2. 同目录下创建calendar_day_titles_container.xml，使用**水平线性布局**，包含七个上一步创建的星期标题，代表一周七天

~~~xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:weightSum="7"
    android:orientation="horizontal">

    <include layout="@layout/calendar_day_title_text"/>
    <!-- 重复6次 -->
</LinearLayout>
~~~

3. 将上一步的容器引入fragment_week_view.xml中

~~~xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="vertical">

    <include
        android:id="@+id/titlesContainer"
        layout="@layout/calendar_day_titles_container" />

    <!-- 周视图日历控件，用于显示一周的日期 -->
    <com.kizitonwose.calendar.view.WeekCalendarView
        android:id="@+id/week_calendar_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:cv_dayViewResource="@layout/calendar_day_layout"/>

</LinearLayout>
~~~

4. 在 WeekViewFragment.kt 中，通过 setDayOfWeekTitles() 方法动态设置星期标题文本

~~~kotlin
...
private fun setupWeekView(){
        ...
        
        val weekCalendarView = binding.weekCalendarView
        weekCalendarView.dayBinder = weekViewBinder
        
        // 获取标题容器并设置星期标题
        setDayOfWeekTitles()
        
        ...
    }

private fun setDayOfWeekTitles() {
    // 获取标题容器
    val titlesContainer = binding.root.findViewById<ViewGroup>(R.id.titlesContainer)
    
    // 遍历每个标题容器并设置文本
    titlesContainer.children
        .map { it as TextView }
        .forEachIndexed { index, textView ->
            val dayOfWeek = daysOfWeek()[index]
            val title = dayOfWeek.getDisplayName(TextStyle.SHORT, Locale.getDefault())
            textView.text = title
            textView.gravity = Gravity.CENTER
        }
}
~~~

至此，添加星期标题功能完成

#### 添加日期选择功能

1. 在presentation/calendarContainer/下的DayViewContainer.kt中**添加**点击监听器

~~~kotlin
class DayViewContainer(view: View) : ViewContainer(view) {
    ...

    // 添加一个函数来设置点击监听器
    fun setOnClickListener(listener: (View) -> Unit) {
        textView.setOnClickListener(listener)
    }

    ...
}
~~~

其中listener参数是一个**函数类型**，是以View为参数，且返回值为Unit(void,即无返回值)的函数

2. 在presentation/calendarBinder/WeekViewBinder.kt中**设置**点击监听器

~~~kotlin
class WeekViewBinder: WeekDayBinder<DayViewContainer> {
    
    ...

    override fun bind(
        container: DayViewContainer,
        data: WeekDay
    ) {
        container.textView.text = data.date.dayOfMonth.toString()

        // 设置点击监听器
        container.setOnClickListener {
            selectedDate = data.date
            onDateClickListener?.invoke(data)
        }

        ...
    }
}
~~~

其中`?.invoke` 是一个**可选调用**，用于调用一个可能为 null 的函数。如果函数不为 null，则调用它，否则不执行任何操作。
这里只有点击日期时才触发点击回调data，并更新选中日期的视觉效果

3. 在 presentation/ui/calendar/WeekViewFragment.kt 中设置点击回调

~~~kotlin
class WeekViewFragment: Fragment() {
    
    ...

    private fun setupWeekView(){
        val currentDate = LocalDate.now()
        
        // 步骤3：在Fragment中设置点击回调
        val weekViewBinder = WeekViewBinder()
        weekViewBinder.onDateClickListener = { weekDay ->
            // 处理点击事件
            Toast.makeText(context, "Clicked: ${weekDay.date}", Toast.LENGTH_SHORT).show()
            // 更新选中日期的视觉效果
            weekViewBinder.selectDate(weekDay.date)
            // 通知日历重新绘制以更新选中状态
            binding.weekCalendarView.notifyCalendarChanged()
        }
        
        val weekCalendarView = binding.weekCalendarView
        weekCalendarView.dayBinder = weekViewBinder
        
        ...

    }
}
~~~

4. 在presentation/calendarBinder/WeekViewBinder.kt中添加选中日期的追踪

~~~kotlin
class WeekViewBinder: WeekDayBinder<DayViewContainer> {

    ...

    override fun bind(
        container: DayViewContainer,
        data: WeekDay
    ) {
        container.textView.text = data.date.dayOfMonth.toString()

        // 设置点击监听器
        container.setOnClickListener {
            selectedDate = data.date
            onDateClickListener?.invoke(data)
        }

        // 更新选中的视觉效果
        if (selectedDate == data.date) {
            container.textView.setBackgroundResource(R.drawable.modern_selected_date_ripple)
            // 选中日期使用白色文字以便在洋红色背景上清晰可见
            container.textView.setTextColor(Color.WHITE)
        } else {
            container.textView.background = null
            // 非选中日期使用黑色文字
            container.textView.setTextColor(Color.BLACK)
        }
    }
}
~~~

这里的背景资源需要自行创建









