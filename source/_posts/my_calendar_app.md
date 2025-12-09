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
      - [添加今日默认背景](#添加今日默认背景)
      - [周的滚动切换](#周的滚动切换)
      - [添加年月标题](#添加年月标题)
  - [日程添加](#日程添加)
    - [点击按钮打开日程添加表单](#点击按钮打开日程添加表单)
    - [日程列表实现](#日程列表实现)
    - [日程修改实现](#日程修改实现)
    - [日程删除实现](#日程删除实现)
    - [日程切换完成状态实现](#日程切换完成状态实现)
    - [日程提醒实现](#日程提醒实现)
    - [日程排序展示](#日程排序展示)
    - [日视图日期切换](#日视图日期切换)
    - [日程的导出](#日程的导出)
    - [日程的导入](#日程的导入)
  - [农历实现](#农历实现)
  - [网络订阅](#网络订阅)
    - [周视图节日](#周视图节日)
    - [周视图番组表](#周视图番组表)
      - [第一步：定义数据模型](#第一步定义数据模型)
      - [第二步：网络请求服务](#第二步网络请求服务)
      - [第三步：UI设计](#第三步ui设计)
      - [第四步：UI展示](#第四步ui展示)
      - [第五步：优化缓存](#第五步优化缓存)


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

#### 添加今日默认背景

[回到上一级](#周视图功能细分)

1. 在presentation/calendarBinder/WeekViewBinder.kt的bind()方法中获取今天的日期

~~~kotlin
val today = LocalDate.now()
~~~

2. 在presentation/calendarBinder/WeekViewBinder.kt的bind()方法中判断当前日期是否为今天，如果是则设置背景

完整方法
~~~kotlin
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

        // 获取今天的日期
        val today = LocalDate.now()
        
        // 更新选中的视觉效果
        if (selectedDate == data.date) {
            container.textView.setBackgroundResource(R.drawable.modern_selected_date_ripple)
            // 选中日期使用白色文字以便在洋红色背景上清晰可见
            container.textView.setTextColor(Color.WHITE)
        } else if (data.date == today) {
            // 今天的日期使用与选中日期相同形状的背景高亮显示
            container.textView.setBackgroundResource(R.drawable.modern_today_background)
            container.textView.setTextColor(Color.BLACK)
        } else {
            container.textView.background = null
            // 非选中日期使用黑色文字
            container.textView.setTextColor(Color.BLACK)
        }
    }
~~~

背景资源需要自行创建

#### 周的滚动切换

[回到上一级](#周视图功能细分)

1. 在WeekViewFragment.kt中的setupWeekView()方法内添加滚动监听器，并实现更新逻辑

* 添加**滚动监听器**
~~~kotlin
private fun setupWeekView(){
        ...
        
        val weekCalendarView = binding.weekCalendarView
        weekCalendarView.dayBinder = weekViewBinder

        // 添加滚动监听器以同步星期标题
        weekCalendarView.weekScrollListener = { weekStart ->
            // 更新星期标题以匹配当前周
            updateDayOfWeekTitles(weekStart.days.first().date)
            // 更新年月标题
            // updateMonthYearHeader(weekStart.days.first().date)
        }

        // 获取标题容器并设置星期标题
        setDayOfWeekTitles()
        
        ...
    }
~~~

* 实现更新逻辑 **updateDayOfWeekTitles()**
~~~kotlin
/**
     * 更新星期标题
     *
     * @param date 当前周的起始日期
     */
    private fun updateDayOfWeekTitles(date: LocalDate) {
        val titlesContainer = binding.root.findViewById<ViewGroup>(R.id.titlesContainer)

        // 计算指定日期所在周到起始日期
        val firstDayOfWeek = firstDayOfWeekFromLocale()
        val weekStart = date.minusDays(date.dayOfWeek.ordinal.toLong())
            .plusDays(firstDayOfWeek.ordinal.toLong())
            .minusDays(if (firstDayOfWeek.ordinal <= date.dayOfWeek.ordinal) 0 else 7)

        // 更新每个标题党文本
        titlesContainer.children
            .map { it as TextView }
            .forEachIndexed { index, textView ->
                val currentDay = weekStart.plusDays(index.toLong())
                val title = currentDay.dayOfWeek.getDisplayName(TextStyle.SHORT, Locale.getDefault())
                textView.text = title
            }
    }
~~~

其中,计算逻辑：
  1. date.dayOfWeek.ordinal：获取指定日期是星期几（星期一为0，星期日为6）
  2. date.minusDays(date.dayOfWeek.ordinal.toLong())：从指定日期减去对应的天数，得到该周的星期一
  3. plusDays(firstDayOfWeek.ordinal.toLong())：加上本地化星期起始日的偏移量
  4. minusDays(if (firstDayOfWeek.ordinal <= date.dayOfWeek.ordinal) 0 else 7)：如果本地化星期起始日在指定日期的星期几之前或当天，则不需要调整否则需要减去7天以得到正确的周起始日

2. 在fragment_week_view.xml中的周视图日历控件中添加属性，优化滑动效果

~~~xml
<!-- 将app:cv_scrollPaged="true"添加到WeekCalendarView控件中 -->
<com.kizitonwose.calendar.view.WeekCalendarView
        android:id="@+id/week_calendar_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:cv_dayViewResource="@layout/calendar_day_layout"
        app:cv_scrollPaged="true"/>
~~~

#### 添加年月标题

[回到上一级](#周视图功能细分)

1. 首先需要修改 fragment_week_view.xml 文件，添加年月标题：

~~~xml
<!-- 添加年月标题 -->
    <TextView
        android:id="@+id/monthYearHeader"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:textSize="18sp"
        android:textStyle="bold"
        android:padding="8dp"
        android:textColor="@color/black"
        tools:text="2023年10月" />
~~~

2. 在 WeekViewFragment.kt 中添加年月标题的处理逻辑：

* 在类中添加年月标题变量声明
~~~kotlin
// 添加年月标题变量声明
private lateinit var monthYearHeader: TextView
~~~

* 在 setupWeekView() 方法中初始化年月标题变量
~~~kotlin
// 初始化年月标题
monthYearHeader = binding.root.findViewById<TextView>(R.id.monthYearHeader)
~~~

* 在 setupWeekView() 方法中的滚动监听器中同步更新年月标题
~~~kotlin
// 添加滚动监听器以同步星期标题
weekCalendarView.weekScrollListener = { weekStart ->
    // 更新星期标题以匹配当前周
    updateDayOfWeekTitles(weekStart.days.first().date)
    // 更新年月标题
    updateMonthYearHeader(weekStart.days.first().date)
    }
~~~

3. 实现更新逻辑 **updateMonthYearHeader()**

~~~kotlin
/**
 * 更新年月标题
 *
 * @param date 当前周的起始日期
 */
private fun updateMonthYearHeader(date: LocalDate) {
    val formatter = DateTimeFormatter.ofPattern("yyyy年MM月")
    val monthYearText = date.format(formatter)

    // 如果文本没变化，不更新
    if (monthYearHeader.text.toString() == monthYearText){
        return
    }

    monthYearHeader.text = monthYearText
}
~~~

## 日程添加

[回到目录](#目录)

### 点击按钮打开日程添加表单

[回到上一级](#日程添加)

1. 在layout中准备表单界面 dialog_add_event.xml

~~~xml
<?xml version="1.0" encoding="utf-8"?>

<!-- 添加日程表格 -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:padding="16dp">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="添加日程"
        android:textSize="20sp"
        android:textStyle="bold"
        android:gravity="center"
        android:layout_marginBottom="16dp"/>

    <com.google.android.material.textfield.TextInputLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="8dp">

        <com.google.android.material.textfield.TextInputEditText
            android:id="@+id/editTextDate"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="日期"
            android:focusable="false"
            android:clickable="true"/>
    </com.google.android.material.textfield.TextInputLayout>

    <com.google.android.material.textfield.TextInputLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="8dp">

        <com.google.android.material.textfield.TextInputEditText
            android:id="@+id/editTextTitle"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="日程标题"/>
    </com.google.android.material.textfield.TextInputLayout>

    <com.google.android.material.textfield.TextInputLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="8dp">

        <com.google.android.material.textfield.TextInputEditText
            android:id="@+id/editTextContent"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="日程内容"
            android:minLines="3"/>
    </com.google.android.material.textfield.TextInputLayout>

    <com.google.android.material.textfield.TextInputLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="16dp">

        <com.google.android.material.textfield.TextInputEditText
            android:id="@+id/editTextReminder"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="提醒时间"
            android:focusable="false"
            android:clickable="true"/>
    </com.google.android.material.textfield.TextInputLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:gravity="end">

        <Button
            android:id="@+id/buttonCancel"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="取消"
            style="@style/Widget.MaterialComponents.Button.TextButton"/>

        <Button
            android:id="@+id/buttonConfirm"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="确认"
            style="@style/Widget.MaterialComponents.Button.TextButton"/>
    </LinearLayout>

</LinearLayout>
~~~

* 其中，TextInputLayout 相比于传统的 EditText 增加了很多功能
  * 浮动标签：提示词在输入内容后不会消失，而是显示于左上方中
  * 错误提示：可以方便地显示错误信息，帮助用户理解输入要求
  * 辅助文本：支持显示帮助文本或字符计数等辅助信息
  * 图标支持：可以在文本框内添加前缀、后缀图标
* 每个 TextInputLayout 都围起一个 TextInputEditText 子元素
  * 属性 focusable="false" 表明该元素不能获得焦点，即不通过键盘直接输入
  * 属性 clickable="true" 表明该元素可以通过点击触发，通过触发事件，间接输入到输入框中

2. 在MainActivity中添加点击按钮打开表单的逻辑

   1. 在onCreate()方法中调用 **setupFloatingActionButton()** 方法
   2. 在MainActivity类内实现 **setupFloatingActionButton()** 方法

        ~~~kotlin
        private fun setupFloatingActionButton() {
            binding.fabAddEvent.setOnClickListener {
                showAddEventDialog()
            }
        }
        ~~~
        * 绑定FAB按钮触发点击事件
        * 通过点击事件触发显示表单方法 **showAddEventDialog()**

   3. 实现 showAddEventDialog() 方法
      1. 创建对话框
        ~~~kotlin
            private fun showAddEventDialog() {
                
                // 1. 创建对话框
                val builder = AlertDialog.Builder(this)
                val inflater = layoutInflater
                val dialogView = inflater.inflate(R.layout.dialog_add_event, null)
                builder.setView(dialogView)

                val dialog = builder.create()
        ~~~
        * 使用 AlertDialog.Builder 创建**对话框构建器**
        * 通过 layoutInflater 加载 dialog_add_event.xml 布局文件
        * 将加载的视图设置为对话框的内容视图
        * 通过 builder.create() 创建**实际的对话框对象**

      2. 获取对话框中的UI组件
      ~~~kotlin
              // 2. 查找对话框中的UI组件
              val editTextDate = dialogView.findViewById<TextInputEditText>(R.id.editTextDate)
              val editTextTitle = dialogView.findViewById<TextInputEditText>(R.id.editTextTitle)
              val editTextContent = dialogView.findViewById<TextInputEditText>(R.id.editTextContent)
              val editTextReminder = dialogView.findViewById<TextInputEditText>(R.id.editTextReminder)
              val buttonCancel = dialogView.findViewById<Button>(R.id.buttonCancel)
              val buttonConfirm = dialogView.findViewById<Button>(R.id.buttonConfirm)
      ~~~
      3. 设置默认日期和日期选择器
      ~~~kotlin
              // 3. 设置默认日期为今天，若有选择的日期，则使用选择的日期
              val selectedDate = getSelectedDate()
              val defaultDate = selectedDate ?: LocalDate.now() // 若无选择日期，则使用今天
              val formatter = DateTimeFormatter.ofPattern("yyyy年MM月dd日") // 日期格式
              editTextDate.setText(defaultDate.format(formatter)) // 设置默认日期

              // 设置日期选择器
              editTextDate.setOnClickListener {
                  showDatePicker(editTextDate)
              }

              // 设置提醒时间选择器
              editTextReminder.setOnClickListener {
                  showTimePicker(editTextReminder)
              }
      ~~~
      4. 设置按钮并显示对话框
      ~~~kotlin
              // 设置取消按钮
              buttonCancel.setOnClickListener {
                  dialog.dismiss()
              }

              // 设置确认按钮
              buttonConfirm.setOnClickListener {
                  val title = editTextTitle.text.toString()
                  val content = editTextContent.text.toString()
                  val date = editTextDate.text.toString()
                  val reminder = editTextReminder.text.toString()

                  // 处理添加事件逻辑
                  Toast.makeText(this, "日程已添加", Toast.LENGTH_SHORT).show()
                  dialog.dismiss()
              }
              
              // 显示对话框
              dialog.show()
          }
      ~~~

   4. 实现 showDatePicker() 方法和 showTimePicker() 方法
      1. 实现 showTimePicker() 方法
      ~~~kotlin
          private fun showTimePicker(editTextReminder: TextInputEditText) {
              // 创建时间选择器对象
              val timePicker = TimePickerDialog(
                  this,
                  {_, hourOfDay, minute ->
                      val selectedTime = String.format("%02d:%02d", hourOfDay, minute) // 将时间格式化为HH:mm
                      editTextReminder.setText(selectedTime) // 设置选中的时间
                  },
                  9, // 默认选中的时间为9:00
                  0,
                  true // 是否使用24小时制
              )
              timePicker.show()
          }
      ~~~

      2. 实现 showDatePicker() 方法

      ~~~kotlin
          private fun showDatePicker(editTextDate: TextInputEditText) {
              val today = LocalDate.now()
              val datePicker = DatePickerDialog(
                  this,
                  {_, year, month, dayOfMonth ->
                      val selectedDate = LocalDate.of(year, month + 1, dayOfMonth) // 创建选中日期的 LocalDate 对象（需要将月份+1，因为DatePicker的月份是从0开始的）
                      val formatter = DateTimeFormatter.ofPattern("yyyy年MM月dd日") // 日期格式
                      editTextDate.setText(selectedDate.format(formatter)) // 显示选中的日期
                  },
                  today.year, 
                  today.monthValue - 1, // month: today.monthValue - 1 - 日期选择器的初始月份设置为当前月份（需要减1，因为DatePicker的月份是从0开始的）
                  today.dayOfMonth
              )
              datePicker.show()
          }
      ~~~

   5. 实现 getSelectedDate() 方法
   ~~~kotlin
       private fun getSelectedDate(): LocalDate?{
           val viewPager = binding.viewPager
           val currentFragment = supportFragmentManager.findFragmentByTag("f" + viewPager.currentItem)

           return when(currentFragment){
               is DayViewFragment -> {
                   // 从日视图中获取选中日期
                   null
               }
               is WeekViewFragment -> {
                   // 从周视图中获取选中日期
                   currentFragment.getSelectedDate()
               }
               is MonthViewFragment -> {
                   // 从月视图中获取选中日期
                   currentFragment.getSelectedDate()
               }
               else -> null
           }
       }
   ~~~
   * 首先通过 binding.viewPager 获取应用中的**ViewPager组件**
   * 通过 viewPager.currentItem 获取当前显示页面的**索引**
   * 使用 supportFragmentManager.findFragmentByTag("f" + viewPager.currentItem) 根据标签查找当前Fragment
     * ViewPager2会自动为每个页面的Fragment分配标签，格式为 **"f"+页面索引**
     * 这样就可以获取到当前显示的具体Fragment实例

3. 在 MonthViewFragment 和 WeekViewFragment 中添加 getSelectedDate() 方法

   * MonthViewFragment 的 getSelectedDate() 方法实现如下：

   ~~~kotlin
       fun getSelectedDate(): LocalDate?{
           val monthViewBinder = binding.monthCalendarView.dayBinder as? MonthDayViewBinder // 将 dayBinder 安全转换为 MonthDayViewBinder
           return  monthViewBinder?.getSelectedDate()
       }
   ~~~


   * WeekViewFragment 的 getSelectedDate() 方法实现如下：
   ~~~kotlin
       fun getSelectedDate(): LocalDate?{
           // 获取 WeekViewBinder 实例并返回其选中的日期
           val weekViewBinder = binding.weekCalendarView.dayBinder as? WeekViewBinder // 将 dayBinder 安全转换为WeekViewBinder
           return weekViewBinder?.getSelectedDate()
       }
   ~~~

4. 在 WeekViewBinder 和 MonthDayViewBinder 中分别实现在其fragment中调用的 getSelectedDate() 方法

* WeekViewBinder 的 getSelectedDate() 方法实现如下：

~~~kotlin
    fun getSelectedDate(): LocalDate?{
        return selectedDate
    }
~~~

* MonthDayViewBinder 的 getSelectedDate() 方法实现如下：

~~~kotlin
    fun getSelectedDate(): LocalDate?{
        return selectedDate
    }
~~~

至此，我们可以通过按钮打开添加日程的表单，并选择日期和时间，添加内容

### 日程列表实现

1. 创建日程卡片视图 item_event_card.xml

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<!-- 日程卡片布局 -->
<com.google.android.material.card.MaterialCardView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_margin="8dp"
    app:cardCornerRadius="12dp"
    app:cardElevation="4dp"
    app:strokeWidth="0dp">

    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="16dp">

        <!-- 事件颜色标识 -->
        <View
            android:id="@+id/eventColorIndicator"
            android:layout_width="6dp"
            android:layout_height="0dp"
            android:background="@color/magenta_500"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent"/>

        <!-- 事件标题 -->
        <TextView
            android:id="@+id/eventTitle"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginStart="12dp"
            android:layout_marginEnd="8dp"
            android:text="事件标题"
            android:textAppearance="@style/TextAppearance.Material3.TitleMedium"
            android:textColor="?attr/colorOnSurface"
            app:layout_constraintEnd_toStartOf="@+id/editEventButton"
            app:layout_constraintStart_toEndOf="@+id/eventColorIndicator"
            app:layout_constraintTop_toTopOf="parent"/>

        <!-- 事件内容摘要 -->
        <TextView
            android:id="@+id/eventContent"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="4dp"
            android:ellipsize="end"
            android:maxLines="1"
            android:text="事件内容摘要，超出十个字显示省略号"
            android:textAppearance="@style/TextAppearance.Material3.BodyMedium"
            android:textColor="?attr/colorOnSurface"
            app:layout_constraintEnd_toEndOf="@+id/eventTitle"
            app:layout_constraintStart_toStartOf="@+id/eventTitle"
            app:layout_constraintTop_toBottomOf="@+id/eventTitle"/>

        <!-- 时间信息 -->
        <TextView
            android:id="@+id/eventTime"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="8dp"
            android:text="持续时间"
            android:textAppearance="@style/TextAppearance.Material3.BodySmall"
            android:textColor="?attr/colorOnSurfaceVariant"
            app:layout_constraintEnd_toEndOf="@+id/eventTitle"
            app:layout_constraintStart_toStartOf="@+id/eventTitle"
            app:layout_constraintTop_toBottomOf="@+id/eventContent"/>

        <!-- 提醒时间 -->
        <TextView
            android:id="@+id/eventReminder"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="4dp"
            android:drawableStart="@drawable/ic_notification"
            android:drawablePadding="4dp"
            android:text="提前15分钟提醒"
            android:textAppearance="@style/TextAppearance.Material3.BodySmall"
            android:textColor="?attr/colorOnSurfaceVariant"
            app:layout_constraintEnd_toEndOf="@+id/eventTitle"
            app:layout_constraintStart_toStartOf="@+id/eventTitle"
            app:layout_constraintTop_toBottomOf="@+id/eventTime" />

        <!-- 编辑按钮 -->
        <ImageButton
            android:id="@+id/editEventButton"
            android:layout_width="36dp"
            android:layout_height="36dp"
            android:layout_marginEnd="8dp"
            android:background="?attr/selectableItemBackgroundBorderless"
            android:contentDescription="编辑事件"
            android:src="@drawable/ic_edit"
            app:layout_constraintEnd_toStartOf="@+id/eventCompletedCheckbox"
            app:layout_constraintTop_toTopOf="parent"
            app:tint="?attr/colorOnSurfaceVariant" />

        <!-- 完成状态复选框 -->
        <CheckBox
            android:id="@+id/eventCompletedCheckbox"
            android:layout_width="36dp"
            android:layout_height="36dp"
            android:layout_marginEnd="8dp"
            app:layout_constraintEnd_toStartOf="@+id/deleteEventButton"
            app:layout_constraintTop_toTopOf="parent" />

        <!-- 删除按钮 -->
        <ImageButton
            android:id="@+id/deleteEventButton"
            android:layout_width="36dp"
            android:layout_height="36dp"
            android:background="?attr/selectableItemBackgroundBorderless"
            android:contentDescription="删除事件"
            android:src="@drawable/ic_delete"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            app:tint="?attr/colorError" />
    </androidx.constraintlayout.widget.ConstraintLayout>
</com.google.android.material.card.MaterialCardView>
~~~
* **MaterialCardView** 是 Google Material Design 组件库中的一个核心 UI 组件，它是一个现代化的**卡片视图**实现

2. 修改日视图 fragment_day_view.xml

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<!--
    日视图 Fragment 的布局文件
    包含标题和日视图内容区域
-->
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:orientation="vertical">

    <!-- 日视图标题文本 -->
    <TextView
        android:id="@+id/dayHeaderText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="16dp"
        android:text="日视图"
        android:textAlignment="center"
        android:textAppearance="@style/TextAppearance.Material3.HeadlineSmall"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"/>

    <!-- 无事件提示 -->
    <TextView
        android:id="@+id/noEventText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="32dp"
        android:text="今天暂无事件"
        android:textAppearance="@style/TextAppearance.Material3.BodyLarge"
        android:textColor="?attr/colorOnSurfaceVariant"
        android:visibility="gone"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/dayHeaderText"/>

    <!-- 事件列表 -->
    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/eventRecyclerView"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:layout_marginTop="16dp"
        android:clipToPadding="false"
        android:paddingBottom="16dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/dayHeaderText"/>
</androidx.constraintlayout.widget.ConstraintLayout>
~~~

* RecyclerView 是 Android 中一个非常重要的 UI 组件，用于在有限的窗口中展示大量数据列表
* RecyclerView 需要 Adapter 类适配器为其绑定数据源
* Adapter 的内部类 ViewHolder 可以持有RecyclerView 的子项视图

3. 创建适配器为 RecyclerView 绑定数据

~~~kotlin
/**
 * 事件卡片适配器类
 * 用于在 RecyclerView 中显示日程事件列表，每个事件以卡片形式展示
 *
 * @param events 初始事件列表
 * @param onEditClick 编辑按钮点击回调函数
 * @param onDeleteClick 删除按钮点击回调函数
 * @param onCompleteToggle 完成状态切换回调函数
 */
class EventCardAdapter(
    private var events: List<CalendarEvent>, // 事件列表数据
    private val onEditClick: (CalendarEvent) -> Unit, // 编辑事件回调
    private val onDeleteClick: (CalendarEvent) -> Unit, // 删除事件回调
    private val onCompleteToggle: (CalendarEvent, Boolean) -> Unit // 完成状态切换回调
): RecyclerView.Adapter<EventCardAdapter.EventViewHolder>() {
    /**
     * 创建 ViewHolder 实例
     *
     * @param parent 父 ViewGroup
     * @param viewType 视图类型
     * @return 返回新创建的 EventViewHolder 实例
     */
    override fun onCreateViewHolder(
        parent: ViewGroup,
        viewType: Int
    ): EventCardAdapter.EventViewHolder {
        val view = LayoutInflater.from(parent.context)
            .inflate(R.layout.item_event_card, parent, false) // 从布局文件创建视图
        return EventViewHolder(view)
    }

    /**
     * 绑定数据到 ViewHolder
     *
     * @param holder 要绑定数据的 ViewHolder
     * @param position 数据在列表中的位置
     */
    override fun onBindViewHolder(holder: EventCardAdapter.EventViewHolder, position: Int) {
        holder.bind(events[position]) // 绑定指定位置的数据到 ViewHolder
    }

    /**
     * 获取事件列表的大小
     *
     * @return 返回事件列表的大小
     */
    override fun getItemCount(): Int {
        return events.size // 返回事件列表大小
    }

    /**
     * 更新事件列表数据
     *
     * @param newEvents 新的事件列表
     */
    fun updateEvents(newEvents: List<CalendarEvent>){
        events = newEvents
        notifyDataSetChanged() // 通知数据变更，刷新界面
    }

    /**
     * 事件卡片 ViewHolder 类
     * 用于持有和管理事件卡片中的各个 UI 元素
     */
    inner class EventViewHolder(itemView: View): RecyclerView.ViewHolder(itemView){
        private val colorIndicator: View = itemView.findViewById(R.id.eventColorIndicator) // 事件颜色指示器
        private val titleText: TextView = itemView.findViewById(R.id.eventTitle) // 事件标题文本
        private val contentText: TextView = itemView.findViewById(R.id.eventContent) // 事件内容文本
        private val timeText: TextView = itemView.findViewById(R.id.eventTime) // 事件时间文本
        private val reminders: TextView = itemView.findViewById(R.id.eventReminder) // 提醒时间文本
        private val editButton: ImageButton = itemView.findViewById(R.id.editEventButton) // 编辑按钮
        private val completeCheckbox: CheckBox = itemView.findViewById(R.id.eventCompletedCheckbox) // 完成状态复选框
        private val deleteButton: ImageButton = itemView.findViewById(R.id.deleteEventButton) // 删除按钮

        /**
         * 绑定事件数据到视图元素
         *
         * @param event 要绑定的事件数据
         */
        fun bind(event: CalendarEvent){
            // 设置事件颜色
            colorIndicator.setBackgroundColor(event.eventColor) // 根据事件颜色设置指示器背景色

            // 设置事件标题
            titleText.text = event.title // 显示事件标题

            // 设置内容摘要(超过十个字省略后面的字)
            contentText.text = if (event.content.length > 10){
                "${event.content.take(10)}..." // 截取前10个字符并添加省略号
            }else{
                event.content // 内容不足10个字符则完整显示
            }

            // 设置时间
            val startTime = formatTime(event.startTime) // 格式化开始时间
            val endTime = formatTime(event.endTime) // 格式化结束时间
            timeText.text = "$startTime - $endTime" // 显示时间范围

            // 设置提醒时间
            reminders.text = formatReminderTime(event.reminderTime) // 显示提醒时间

            // 设置完成状态
            completeCheckbox.isChecked = event.isCompleted // 根据事件状态设置复选框选中状态

            // 设置点击事件
            editButton.setOnClickListener { onEditClick(event) } // 点击编辑按钮触发回调
            deleteButton.setOnClickListener { onDeleteClick(event) } // 点击删除按钮触发回调
            completeCheckbox.setOnCheckedChangeListener { _, isChecked ->
                onCompleteToggle(event, isChecked) // 切换完成状态时触发回调
            }
        }

        /**
         * 格式化时间戳为 HH:mm 格式的字符串
         *
         * @param timeStamp 时间戳
         * @return 格式化后的时间字符串，如果时间戳为0则返回"无"
         */
        private fun formatTime(timeStamp: Long): String{
            return if (timeStamp == 0L){
                "无" // 时间戳为0时显示"无"
            }else{
                val date = Date(timeStamp)
                val format = SimpleDateFormat("HH:mm", Locale.getDefault()) // 使用 HH:mm 格式
                format.format(date) // 格式化时间戳
            }
        }

        /**
         * 格式化提醒时间戳为 HH:mm 格式的字符串
         *
         * @param timeStamp 提醒时间戳
         * @return 格式化后的提醒时间字符串，如果时间戳为0则返回"无"
         */
        private fun formatReminderTime(timeStamp: Long): String{
            return if (timeStamp == 0L){
                "无" // 时间戳为0时显示"无"
            }else{
                val date = Date(timeStamp)
                val format = SimpleDateFormat("HH:mm", Locale.getDefault()) // 使用 HH:mm 格式
                format.format(date) // 格式化时间戳
            }
        }
    }
}
~~~
* 实现 ViewHolder 模式
* 绑定数据到视图
* 处理用户交互（增加、修改、删除）

4. 准备图标按钮需要的矢量图标资源

* 删除按钮图标 ic_delete.xml

~~~xml
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="24dp"
    android:height="24dp"
    android:viewportWidth="24"
    android:viewportHeight="24"
    android:tint="?attr/colorError">
    <path
        android:fillColor="@android:color/white"
        android:pathData="M6,19c0,1.1 0.9,2 2,2h8c1.1,0 2,-0.9 2,-2V7H6v12zM19,4h-3.5l-1,-1h-5l-1,1H5v2h14V4z"/>
</vector>
~~~

* 编辑按钮图标 ic_edit.xml

~~~xml
<!-- res/drawable/ic_edit.xml -->
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="24dp"
    android:height="24dp"
    android:viewportWidth="24"
    android:viewportHeight="24">
    <path
        android:fillColor="#FF000000"
        android:pathData="M3,17.25V21h3.75L17.81,9.94l-3.75,-3.75L3,17.25zM20.71,7.04c0.39,-0.39 0.39,-1.02 0,-1.41l-2.34,-2.34c-0.39,-0.39 -1.02,-0.39 -1.41,0l-1.83,1.83 3.75,3.75 1.83,-1.83z"/>
</vector>
~~~

* 确认完成图标 ic_notification.xml

~~~xml
<!-- res/drawable/ic_notification.xml -->
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="24dp"
    android:height="24dp"
    android:viewportWidth="24"
    android:viewportHeight="24"
    android:tint="?attr/colorOnSurfaceVariant">
    <path
        android:fillColor="@android:color/white"
        android:pathData="M12,22c1.1,0 2,-0.9 2,-2h-4c0,1.1 0.89,2 2,2zM18,16v-5c0,-3.07 -1.64,-5.64 -4.5,-6.32L13.5,4c0,-0.83 -0.67,-1.5 -1.5,-1.5s-1.5,0.67 -1.5,1.5v0.68C7.63,5.36 6,7.92 6,11v5l-2,2v1h16v-1l-2,-2z"/>
</vector>
~~~

5. 修改日视图 DayViewFragment.kt 以初始化适配器，并启动数据库，为适配器设置数据源

~~~kotlin
/**
 * 日视图 Fragment
 * 负责显示和管理日历的日视图，显示特定日期的详细信息
 */
class DayViewFragment: Fragment() {
    private var _binding: FragmentDayViewBinding? = null
    private val binding get() = _binding!!

    private lateinit var dayTitle: TextView
    private lateinit var noEventsText: TextView
    private lateinit var eventsRecyclerView: RecyclerView
    private lateinit var eventAdapter: EventCardAdapter

    /**
     * 事件更新观察者
     * 当收到事件更新通知时，重新加载当天的事件列表
     */
    private val eventUpdateObserver = Observer<Boolean> { _ ->
        // 当收到事件更新时，刷新日程列表
        loadEventsForDate()
    }

    /**
     * 创建 Fragment 的视图层次结构
     *
     * @param inflater 用于 inflate 视图的 LayoutInflater
     * @param container 此 Fragment 的父 ViewGroup
     * @param savedInstanceState 保存的状态信息，如果 Fragment 是重新创建的，则不为 null
     * @return 返回此 Fragment 的根视图
     */
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        _binding = FragmentDayViewBinding.inflate(inflater, container, false)
        val view = _binding!!.root

        dayTitle = binding.dayHeaderText
        noEventsText = binding.noEventText
        eventsRecyclerView = binding.eventRecyclerView

        return view
    }

    /**
     * 设置 RecyclerView 及其适配器
     * 初始化事件列表的显示组件
     */
    private fun setupRecyclerView() {
        eventAdapter = EventCardAdapter(
            events = emptyList(),
            onEditClick = { event ->
                editEvent(event)
            },
            onDeleteClick = { event ->
                deleteEvent(event)
            },
            onCompleteToggle = { event, isCompleted ->
                toggleEventCompletion(event, isCompleted)
            }
        )

        eventsRecyclerView.apply {
            layoutManager = LinearLayoutManager(context)
            adapter = eventAdapter
        }
    }

    /**
     * 加载指定日期的事件
     * 触发从数据库加载事件数据的操作
     */
    private fun loadEventsForDate(){
        loadEventFromDatabase()
    }

    /**
     * 更新事件列表显示
     * 根据事件列表是否为空来控制空提示文本的显示
     *
     * @param events 要显示的事件列表
     */
    private fun updateEventList(events: List<CalendarEvent>){
        eventAdapter.updateEvents(events)
        noEventsText.visibility = if (events.isEmpty()) View.VISIBLE else View.GONE
    }

    /**
     * 从数据库加载事件数据
     * 异步查询数据库获取当天的所有事件，并更新UI显示
     */
    private fun loadEventFromDatabase(){
        lifecycleScope.launch {
            try {
                // 获取数据库实例
                val database = CalendarDatabase.getInstance(requireContext())
                val eventDao = database.eventDao()

                // 计算当天的开始和结束日期
                val currentDate = LocalDate.now()
                val startOfDay = currentDate.atStartOfDay()
                val endOfDay = currentDate.atTime(23, 59, 59)

                val startTimeStamp = startOfDay.toInstant(ZoneOffset.UTC).toEpochMilli()
                val endTimeStamp = endOfDay.toInstant(ZoneOffset.UTC).toEpochMilli()

                // 查询数据库并获取事件
                val events = eventDao.getEventsInRange(startTimeStamp, endTimeStamp)

                // 更新UI
                updateEventList(events)
            } catch (e: Exception){
                // 处理异常
                Toast.makeText(context, "加载事件失败：${e.message}", Toast.LENGTH_SHORT).show()
            }
        }
    }

    /**
     * 编辑指定事件
     *
     * @param event 需要编辑的事件对象
     */
    private fun editEvent(event: CalendarEvent) {
        // 添加编辑事件逻辑
    }
    
    /**
     * 删除指定事件
     *
     * @param event 需要删除的事件对象
     */
    private fun deleteEvent(event: CalendarEvent) {
        // 添加删除事件逻辑
    }
    
    /**
     * 切换事件完成状态
     *
     * @param event 需要切换状态的事件对象
     * @param isCompleted 事件的完成状态
     */
    private fun toggleEventCompletion(event: CalendarEvent, isCompleted: Boolean) {
        // 添加切换事件完成状态逻辑
    }

    /**
     * 当 Fragment 的视图创建完成后调用
     * 在这里进行视图相关的初始化操作
     *
     * @param view 此 Fragment 的根视图
     * @param savedInstanceState 保存的状态信息
     */
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        // 观察事件更新
        EventUpdateManager.getInstance().eventUpdated.observe(
            viewLifecycleOwner,
            eventUpdateObserver
        )

        
        // 初始化 ViewModel
        val database = CalendarDatabase.getInstance(requireContext())
        val eventDao = database.eventDao()
        
        setupRecyclerView()
        
        // 加载初始数据
        loadEventsForDate()
        setupDayView()
    }

    /**
     * 设置日视图的相关配置
     * 包括设置当前日期标题等
     */
    private fun setupDayView(){
        val currentDate = LocalDate.now()
        // 设置日视图头部显示的日期文本
        binding.dayHeaderText.text = DateTimeFormatter.ofPattern("yyyy年MM月dd日").format(currentDate)
    }

    /**
     * 当 Fragment 的视图被销毁时调用
     * 清理资源，避免内存泄漏
     */
    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}
~~~
* 初始化界面组件
* 设置 RecyclerView 和适配器
* 从数据库加载当天事件数据
* 更新界面显示
* 使用 Room 数据库查询当天事件
* 计算查询时间范围（当天开始到结束）
* 异步加载数据并更新 UI
* 处理空数据状态显示
* 声明观察者，并在 **OnViewCreated** 中激活观察者，通过 **EventUpdateManager.getInstance().eventUpdated.observe()** 来监听 **eventUpdated** 的改变

6. 创建事件管理类 EventUpdateManager.kt 使用 LiveData 机制，监听数据更新，刷新UI

~~~kotlin
/**
 * 事件更新管理器
 * 使用单例模式管理事件更新通知，防止频繁更新UI
 */
class EventUpdateManager private constructor(){
    private val _eventUpdated = MutableLiveData<Boolean>()
    val eventUpdated: LiveData<Boolean> = _eventUpdated

    /**
     * 上次通知时间戳
     * 用于防抖处理，避免过于频繁的更新通知
     */
    private var lastNotifyTime = 0L
    
    /**
     * 防抖间隔时间（毫秒）
     * 两次通知之间的最小时间间隔
     */
    private val debounceInterval = 500L

    /**
     * 通知事件已添加
     * 通过防抖机制控制通知频率，避免短时间内重复通知
     */
    fun notifyEventAdded(){
        val currentTime = System.currentTimeMillis()
        // 如果当前时间间隔小于防抖间隔，则不执行通知
        if (currentTime - lastNotifyTime > debounceInterval){
            _eventUpdated.value = true
            lastNotifyTime = currentTime
        }
    }

    companion object{
        @Volatile
        private var INSTANCE: EventUpdateManager? = null

        /**
         * 获取 EventUpdateManager 的单例实例
         *
         * @return EventUpdateManager 的单例对象
         */
        fun getInstance(): EventUpdateManager{
            return INSTANCE ?: synchronized(this){
                val instance = EventUpdateManager()
                INSTANCE = instance
                instance
            }
        }
    }
}
~~~
* 使用 **LiveData 机制**，可变的_eventUpdated 属性保存事件更新状态，不可变的eventUpdated 属性提供外部访问
* 当事件变化调用 notifyEventAdded() 方法时，LiveData机制会通知所有**活跃的观察者**
* 观察者调用相关方法，更新UI

7. 在主活动 MainActivity.kt 中 **saveEventToDatabase** 方法中，当操作完成后，调用 EventUpdateManager.getInstance().notifyEventAdded() 方法通知事件已添加，通过 LiveData 机制，更新UI

~~~kotlin
// 在主线程中显示成功消息
withContext(Dispatchers.Main){
    Toast.makeText(this@MainActivity, "日程已添加，ID：$eventId", Toast.LENGTH_SHORT).show()

    // 通知日程列表刷新
    EventUpdateManager.getInstance().notifyEventAdded()

}
~~~

### 日程修改实现

[回到上一级](#日程添加)

* 该步骤要实现 **DayViewFragment.kt** 中的 **editEvent()** 方法
* 该方法的传递逻辑为 *editEvent() -> setupRecyclerView() -> onViewCreated()* 
* 通过 EventCardAdapter 类中的 editButton.setOnClickListener() 方法触发调用

~~~kotlin
/**
 * 编辑指定事件
 *
 * @param event 需要编辑的事件对象
 */
private fun editEvent(event: CalendarEvent) {
    // 添加编辑事件逻辑

}
~~~

1. 创建对话框

~~~kotlin
// 创建对话框
val dialogView = LayoutInflater.from(context).inflate(R.layout.dialog_add_event, null)
val dialogBuilder = AlertDialog.Builder(requireContext())
dialogBuilder.setView(dialogView)
~~~

2. 获取对话框的视图元素

~~~kotlin
// 获取对话框的视图元素
val dialogTitle = dialogView.findViewById<TextView>(R.id.dialogTitle)
val eventIdText = dialogView.findViewById<TextView>(R.id.eventIdText)
val editTextDate = dialogView.findViewById<TextInputEditText>(R.id.editTextDate)
val editTextTitle = dialogView.findViewById<TextInputEditText>(R.id.editTextTitle)
val editTextContent = dialogView.findViewById<TextInputEditText>(R.id.editTextContent)
val editTextReminder = dialogView.findViewById<TextInputEditText>(R.id.editTextReminder)
val buttonCancel = dialogView.findViewById<Button>(R.id.buttonCancel)
val buttonConfirm = dialogView.findViewById<Button>(R.id.buttonConfirm)
~~~

3. 对话框视图信息回显

~~~kotlin
// 回显事件信息 - 将当前事件的信息显示在对话框中供用户编辑
eventIdText.text = event.id.toString()
editTextTitle.setText(event.title)
editTextContent.setText(event.content)

// 格式化显示日期 - 将事件开始时间格式化为"yyyy年MM月dd日"格式显示
val eventDate = Date(event.startTime)
val dateFormat = SimpleDateFormat("yyyy年MM月dd日", Locale.getDefault())
editTextDate.setText(dateFormat.format(eventDate))

// 格式化并显示提醒时间 - 如果事件设置了提醒时间，则格式化为"HH:mm"格式显示
val timeFormatter = SimpleDateFormat("HH:mm", Locale.getDefault())
if (event.reminderTime != 0L){
    editTextReminder.setText(timeFormatter.format(Date(event.reminderTime)))
}
~~~

4. 设置日期选择器和时间选择器

~~~kotlin
// 设置日期选择器 - 点击日期输入框时弹出日期选择器
editTextDate.setOnClickListener {
    showDatePicker(editTextDate)
}

// 设置提醒时间选择器 - 点击提醒时间输入框时弹出时间选择器
editTextReminder.setOnClickListener {
    showTimePicker(editTextReminder)
}
~~~

* 日期选择器

~~~kotlin
private fun showDatePicker(editTextDate: TextInputEditText) {
    val today = LocalDate.now()
    val datePicker = DatePickerDialog(
        requireContext(),
        {_, year, month, dayOfMonth ->
            val selectedDate = LocalDate.of(year, month + 1, dayOfMonth)
            val formatter = DateTimeFormatter.ofPattern("yyyy年MM月dd日")
            editTextDate.setText(selectedDate.format(formatter))
        },
        today.year,
        today.monthValue - 1,
        today.dayOfMonth
    )
    datePicker.show()
}
~~~

* 时间选择器

~~~kotlin
private fun showTimePicker(editTextReminder: TextInputEditText) {
    val timePicker = TimePickerDialog(
        requireContext(),
        {_, hourOfDay, minute ->
            val selectedTime = String.format("%02d:%02d", hourOfDay, minute)
            editTextReminder.setText(selectedTime)
        },
        9,
        0,
        true
    )
    timePicker.show()
}
~~~

5. 创建并显示对话框

~~~kotlin
val dialog = dialogBuilder.create()
~~~

6. 设置取消按钮事件

~~~kotlin
buttonCancel.setOnClickListener {
    dialog.dismiss()
}
~~~

7. 设置确认按钮事件

~~~kotlin
buttonConfirm.setOnClickListener {
    // 添加修改事件逻辑
    try {
        // 事件逻辑
    } catch (e: Exception) {
        // 失败处理
    }
}
~~~

* 使用协程作用于执行数据库的耗时操作

~~~kotlin
lifecycleScope.launch {
    // 修改数据库相关操作
}
~~~

* 获取数据库实例和事件数据访问对象

~~~kotlin
val database = CalendarDatabase.getInstance(requireContext())
val eventDao = database.eventDao()
~~~

* 获取修改的数据并解析

~~~kotlin
// 获取用户输入的数据
val title = editTextTitle.text.toString()
val content = editTextContent.text.toString()
val reminder = editTextReminder.text.toString()

// 解析日期 - 将用户输入的日期字符串转换为LocalDate对象
val dateFormatter = DateTimeFormatter.ofPattern("yyyy年MM月dd日")
val localDate = LocalDate.parse(editTextDate.text.toString(), dateFormatter)
// 计算事件开始时间和结束时间的时间戳
val startTime = localDate.atStartOfDay(ZoneId.systemDefault()).toInstant().toEpochMilli()
val endTime = localDate.atTime(23, 59, 59).atZone(ZoneId.systemDefault()).toInstant().toEpochMilli()

// 解析提醒时间 - 如果设置了提醒时间，则计算提醒时间的时间戳
val reminderTime = if (reminder.isNotEmpty()){
    val timeFormatter = DateTimeFormatter.ofPattern("HH:mm")
    val localTime = LocalTime.parse(reminder, timeFormatter)
    val reminderDateTime = localDate.atTime(localTime)
    reminderDateTime.atZone((ZoneId.systemDefault())).toInstant().toEpochMilli()
}else {
    0L // 如果未设置提醒时间，则设为0
}
~~~

* 创建修改事件对象，校验关键信息非空，并提交修改

~~~kotlin
// 更新事件对象 - 使用新的数据创建更新后的事件对象
val updatedEvent = event.copy(
    title = title,
    content = content,
    startTime = startTime,
    endTime = endTime,
    reminderTime = reminderTime
)

// 验证必填字段 - 检查标题是否为空
if (title.isEmpty()){
    editTextTitle.error = "请输入日程标题"
    return@launch
}

// 验证必填字段 - 检查日期是否为空
if (content.isEmpty()){
    editTextDate.error = "请选择日程日期"
    return@launch
}

// 更新事件到数据库
eventDao.updateEvent(updatedEvent)
~~~

* 通知观察者，刷新视图

~~~kotlin
EventUpdateManager.getInstance().notifyEventAdded()
~~~

* 关闭对话框并显示成功提示

~~~kotlin
dialog.dismiss()
Toast.makeText(context, "修改成功", Toast.LENGTH_SHORT).show()
~~~

* 捕获异常并显示错误提示

~~~kotlin
Toast.makeText(context, "修改失败：${e.message}", Toast.LENGTH_SHORT).show()
~~~

8. 展示对话框

~~~kotlin
dialog.show()
~~~

至此，我们可以通过点击修改按钮，弹出修改事件对话框，并完成修改事件操作

### 日程删除实现

[回到上一级](#日程添加)

* 该步骤要实现 **DayViewFragment.kt** 中的 **deleteEvent()** 方法

~~~kotlin
/**
 * 删除指定事件
 *
 * @param event 需要删除的事件对象
 */
private fun deleteEvent(event: CalendarEvent) {
    // 添加删除事件逻辑
}
~~~

1. 创建确认对话框

~~~kotlin
val dialogBuilder = AlertDialog.Builder(requireContext())
dialogBuilder.setTitle("确认删除")
dialogBuilder.setMessage("确定要删除事件 \"${event.title}\" 吗？ 此操作无法撤销。")
~~~

2. 设置确认按钮，并通过协程异步执行数据库操作

~~~kotlin
// 设置确认按钮
dialogBuilder.setPositiveButton("删除"){_, _ ->
    lifecycleScope.launch {
        try {
            // 获取数据库实例
            val database = CalendarDatabase.getInstance(requireContext())
            val eventDao = database.eventDao()

            // 从数据库中删除事件
            eventDao.deleteEvent(event)

            // 通知事件更新
            EventUpdateManager.getInstance().notifyEventAdded()

            // 显示删除成功提示
            Toast.makeText(context, "删除成功", Toast.LENGTH_SHORT).show()
        } catch (e: Exception){
            // 捕获异常并显示错误提示
            Toast.makeText(context, "删除失败：${e.message}", Toast.LENGTH_SHORT).show()
        }
    }
}
~~~

3. 创建取消按钮

~~~kotlin
// 设置取消按钮
dialogBuilder.setNegativeButton("取消"){dialog, _ ->
    dialog.dismiss()
}
~~~

4. 显示对话框

~~~kotlin
dialogBuilder.create().show()
~~~

### 日程切换完成状态实现

[回到上一级](#日程添加)

* 该步骤要实现 **DayViewFragment.kt** 中的 **toggleEventCompletion()** 方法
* 通过协程异步执行数据库操作
* 通过 LiveData 观察者，刷新视图

~~~kotlin
private fun toggleEventCompletion(event: CalendarEvent, isCompleted: Boolean) {
    // 添加切换事件完成状态逻辑
    lifecycleScope.launch {
        try {
            val database = CalendarDatabase.getInstance(requireContext())
            val eventDao = database.eventDao()

            val updatedEvent = event.copy(isCompleted = isCompleted)

            eventDao.updateEvent(updatedEvent)

            // 通知事件更新
            EventUpdateManager.getInstance().notifyEventAdded()
            val message = if(isCompleted) "事件已完成" else "事件标记为未完成"
            Toast.makeText(context, message, Toast.LENGTH_SHORT).show()
        } catch (e: Exception){
            Toast.makeText(context, "切换失败：${e.message}", Toast.LENGTH_SHORT).show()
        }
    }
}
~~~

### 日程提醒实现

[回到上一级](#日程添加)

1. 在 AndroidManifest.xml 中`<manifest>`标签下添加权限

~~~xml
<!-- 铃声提醒服务权限 -->
    <uses-permission android:name="android.permission.VIBRATE"/>
    <uses-permission android:name="android.permission.WAKE_LOCK"/>
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>
    <uses-permission android:name="android.permission.POST_NOTIFICATIONS"/>
    <uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/>
    <uses-permission android:name="android.permission.SCHEDULE_EXACT_ALARM"/>
~~~

* **VIBRATE** - 允许应用控制振动功能
* **WAKE_LOCK** - 允许应用防止处理器进入休眠状态或屏幕变暗
* **RECEIVE_BOOT_COMPLETED** - 允许应用在系统完成启动后接收广播，常用于应用自启动
* **POST_NOTIFICATIONS** - Android 13 (API 33)中引入的权限，允许应用发布通知
* **SYSTEM_ALERT_WINDOW** - 允许应用创建系统级悬浮窗，显示在其他应用之上
* **SCHEDULE_EXACT_ALARM** - 允许应用精确安排警报，常用于定时任务

2. 在domain/manager/下创建日程提醒接收器类 ReminderReceiver.kt

~~~kotlin
class ReminderReceiver: BroadcastReceiver() {
    // 类内逻辑
}
~~~

* 当有事件提醒触发时，该接收器会处理提醒逻辑，包括显示**浮动通知**、**播放提示音**和**振动**等操作

3. 在 ReminderReceiver.kt 类内创建伴生对象，定义了用于传递数据的常量键名

~~~kotlin
companion object{
        /** 事件ID键名 */
        const val EVENT_ID = "event_id"
        /** 事件标题键名 */
        const val EVENT_TITLE = "event_title"
        /** 事件内容键名 */
        const val EVENT_CONTENT = "event_content"
        /** 是否为精确闹钟键名 */
        const val IS_EXACT_ALARM = "is_exact_alarm"
        /** 通知渠道ID */
        private const val CHANNEL_ID = "event_reminder_channel"
        /** 通知渠道名称 */
        private const val CHANNEL_NAME = "Event Reminder"
        /** 通知ID */
        private const val NOTIFICATION_ID = 1001
    }
~~~

4. 在 ReminderReceiver.kt 类内重写 **onReceive()** 方法，接收广播事件并处理提醒逻辑

~~~kotlin
override fun onReceive(context: Context, intent: Intent) {
    val eventId = intent.getLongExtra(EVENT_ID, -1)
    val eventTitle = intent.getStringExtra(EVENT_TITLE) ?: "Event Reminder"
    val eventContent = intent.getStringExtra(EVENT_CONTENT) ?: ""
    val isExactAlarm = intent.getBooleanExtra(IS_EXACT_ALARM, true) // 默认为精确闹钟

    // 显示 Toast
    val reminderType = if (isExactAlarm) "精确" else "非精确"
    Toast.makeText(context, "事件提醒[$reminderType]：$eventTitle", Toast.LENGTH_LONG).show()

    // 显示浮框提醒
    showFloatingNotification(context, eventTitle, eventId)

    // 振动
    vibrate(context)

    // 播放铃声
    playNotificationSound(context)
}
~~~

5. 实现onReceive()方法中的播放铃声方法 playNotificationSound()

~~~kotlin
private fun playNotificationSound(context: Context) {
    try {
        // 获取系统默认通知铃声
        val notificationUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION)

        // 创建MediaPlayer实例并设置数据源
        mediaPlayer = MediaPlayer().apply {
            setDataSource(context, notificationUri)
            // 设置音频流类型
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
                val audioAttributes = AudioAttributes.Builder()
                    .setContentType(AudioAttributes.CONTENT_TYPE_SONIFICATION)
                    .setUsage(AudioAttributes.USAGE_NOTIFICATION)
                    .build()
                setAudioAttributes(audioAttributes)
            } else {
                // 为保持向后兼容，API 21以下版本仍使用旧方法
                @Suppress("DEPRECATION")
                setAudioStreamType(AudioManager.STREAM_NOTIFICATION)
            }
            // 设置循环播放
            isLooping = true
            // 准备并开始播放
            prepareAsync()

            // 设置准备完成的监听器
            setOnPreparedListener {
                start()
            }

            // 设置30秒后停止播放
            Handler(Looper.getMainLooper()).postDelayed({
                stopNotificationSound()
            }, 30000)
        }
    } catch (e: Exception){
        fallbackToRingtone(context)
        e.printStackTrace()
    }
}

/**
 * 备用方案：使用Ringtone播放通知声音
 *
 * @param context 上下文对象
 */
private fun fallbackToRingtone(context: Context) {
    try {
        val notification = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION)
        val ringtone = RingtoneManager.getRingtone(context, notification)
        ringtone.play()
    }catch (e: Exception){
        e.printStackTrace()
    }
}
~~~

* RingtoneManager.getDefaultUri() 方法用于获取**系统默认**的铃声
* 使用 mediaPlayer 实现**循环播放**系统通知铃声，并设置30秒后停止播放
* 若播放失败，则使用 fallbackToRingtone() 方法，使用 RingtoneManager.getRingtone() 方法播放系统默认铃声

6. 实现onReceive()方法中的振动方法 vibrate()

~~~kotlin
/**
 * 设备振动提醒
 *
 * @param context 上下文对象
 */
private fun vibrate(context: Context) {
    val vibrator = context.getSystemService(Vibrator::class.java)
    val vibrationPattern = longArrayOf(0, 500, 200, 500)

    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O){
        val effect = VibrationEffect.createWaveform(vibrationPattern, 0)
        vibrator.vibrate(effect)
    } else{
        @Suppress("DEPRECATION")
        vibrator.vibrate(vibrationPattern, 0)
    }
}
~~~

7. 实现onReceive()方法中的显示浮框提醒方法 showFloatingNotification()

* 创建浮窗提醒布局 notification_floating_layout.xml

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal"
    android:padding="16dp"
    android:background="#CC000000"
    android:gravity="center_vertical">

    <LinearLayout
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:orientation="vertical">

        <TextView
            android:id="@+id/notification_title"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="#FFFFFF"
            android:textSize="16sp"
            android:textStyle="bold" />

        <TextView
            android:id="@+id/notification_time"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="#CCCCCC"
            android:textSize="14sp" />
    </LinearLayout>

    <Button
        android:id="@+id/stop_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="停止"
        android:textColor="#FFFFFF"
        android:background="#FF5555" />
</LinearLayout>
~~~

* 实现显示浮窗提醒方法 showFloatingNotification()

~~~kotlin
/**
 * 显示浮动通知窗口
 *
 * @param context 上下文对象
 * @param eventTitle 事件标题
 * @param eventId 事件ID
 */
private fun showFloatingNotification(
    context: Context,
    eventTitle: String,
    eventId: Long
) {
    // 检查浮框权限
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
        if (!Settings.canDrawOverlays(context)) {
            // 没有权限，只显示通知
            Toast.makeText(context, "请授予悬浮窗权限以显示浮窗提醒", Toast.LENGTH_LONG).show()
            return
        }
    }

    // 创建浮框视图
    val layoutInflater = LayoutInflater.from(context)
    val floatingView = layoutInflater.inflate(R.layout.notification_floating_layout, null)

    // 设置浮框参数
    val params = WindowManager.LayoutParams(
        WindowManager.LayoutParams.MATCH_PARENT,
        WindowManager.LayoutParams.WRAP_CONTENT,
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) WindowManager.LayoutParams.TYPE_APPLICATION_OVERLAY
        else {
            @Suppress("DEPRECATION")
            WindowManager.LayoutParams.TYPE_PHONE
        },
        WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE,
        PixelFormat.TRANSLUCENT
    )

    params.gravity = Gravity.TOP
    params.x = 0
    params.y = 100

    // 获取 WindowManager
    val windowManager = context.getSystemService(Context.WINDOW_SERVICE) as WindowManager

    // 添加浮框到窗口
    windowManager.addView(floatingView, params)

    // 设置标题和时间
    val titleTextView = floatingView.findViewById<TextView>(R.id.notification_title)
    val timeTextView = floatingView.findViewById<TextView>(R.id.notification_time)
    val stopButton = floatingView.findViewById<Button>(R.id.stop_button)

    titleTextView.text = eventTitle
    timeTextView.text = SimpleDateFormat("HH:mm", Locale.getDefault()).format(Date())

    // 设置停止按钮点击事件
    stopButton.setOnClickListener {
        // 停止振动
        @Suppress("DEPRECATION")
        val vibrator = context.getSystemService(Context.VIBRATOR_SERVICE) as Vibrator
        vibrator.cancel()

        // 停止播放铃声
        stopNotificationSound()

        // 移除浮框
        windowManager.removeView(floatingView)
    }

    // 5分钟后自动移除浮框并停止振动
    Handler(Looper.getMainLooper()).postDelayed({
        try {
            // 停止振动
            @Suppress("DEPRECATION")
            val vibrator = context.getSystemService(Context.VIBRATOR_SERVICE) as Vibrator
            vibrator.cancel()

            // 移除浮框
            windowManager.removeView(floatingView)
        } catch (e: Exception) {
            // 忽略异常
        }
    }, 5 * 60 * 1000) // 5分钟
}
~~~

8. 在 domain/manager 下创建闹钟提醒管理器 AlarmReminderManager.kt 管理创建闹钟提醒和取消闹钟提醒

~~~kotlin
class AlarmReminderManager(private val context: Context) {
    // 创建提醒和取消提醒方法
}
~~~

* 创建提醒方法 setReminder() 
实现
~~~kotlin
fun setReminder(event: CalendarEvent){
    if(event.reminderTime <= 0) return

    val alarmManager = context.getSystemService(Context.ALARM_SERVICE) as AlarmManager
    val isExactAlarm = if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S){
        alarmManager.canScheduleExactAlarms()
    }else{
        true
    }

    val intent = Intent(context, ReminderReceiver::class.java).apply {
        putExtra(ReminderReceiver.EVENT_ID, event.id)
        putExtra(ReminderReceiver.EVENT_TITLE, event.title)
        putExtra(ReminderReceiver.EVENT_CONTENT, event.content)
        putExtra(ReminderReceiver.IS_EXACT_ALARM, isExactAlarm) // 传递提醒类型信息
    }

    val pendingIntent = PendingIntent.getBroadcast(
        context,
        event.id.toInt(),
        intent,
        PendingIntent.FLAG_UPDATE_CURRENT or PendingIntent.FLAG_IMMUTABLE
    )

    if (isExactAlarm){
        // 精确提醒
        alarmManager.setExact(AlarmManager.RTC_WAKEUP, event.reminderTime, pendingIntent)
    }else{
        // 模糊提醒
        alarmManager.setWindow(
            AlarmManager.RTC_WAKEUP,
            event.reminderTime,
            event.reminderTime + 5 * 60 * 1000,
            pendingIntent
        )
    }
}
~~~
* 使用 **canScheduleExactAlarms()** 方法检查是否支持精确提醒，支持则使用精确提醒，否则使用模糊提醒
* 通过 **putExtra()** 方法传递以键值对的方式传递的提醒信息
* 通过 Intent 的**显式指定**广播接收者 ReminderReceiver
* 通过**延迟执行**的 PendingIntent 创建一个广播请求
* 以事件id为请求码，确保广播的**唯一性**
* 通过 AlarmManager 在**指定时间触发** PendingIntent 系统会确保将广播发送给指定的接收者

---

* 创建取消提醒方法 cancelReminder()

~~~kotlin
fun cancelReminder(context: Context, eventId: Long){
    val alarmManager = context.getSystemService(Context.ALARM_SERVICE) as AlarmManager
    val intent = Intent(context, ReminderReceiver::class.java)
    val pendingIntent = PendingIntent.getBroadcast(
        context,
        eventId.toInt(),
        intent,
        PendingIntent.FLAG_UPDATE_CURRENT or PendingIntent.FLAG_IMMUTABLE
    )
    alarmManager.cancel(pendingIntent)
}
~~~
* alarmManager通过唯一的请求码取消指定的PendingIntent

9. 在 AndroidManifest.xml 中`<manifest>/<application>`标签下注册广播接收者 ReminderReceiver
~~~xml
<!-- 添加广播接收器声明 -->
<receiver android:name=".domain.manager.ReminderReceiver"
    android:enabled="true"
    android:exported="false"/>
~~~

10. 在增删改事件时调用 AlarmReminderManager 管理创建和取消提醒

* 在MainActivity的saveEventToDatabase()方法中，添加日程提醒

~~~kotlin
//...其他代码
// 获取数据库实例并插入事件
val database = CalendarDatabase.getInstance(this@MainActivity)
val eventId = database.eventDao().insertEvent(event)

// 设置提醒
val alarmReminderManager = AlarmReminderManager(applicationContext)
if (reminderTime > 0){
    alarmReminderManager.setReminder(event)
}
//...其他代码
~~~

* 在DayViewFragment中的editEvent()方法下，先取消原来事件的提醒，在设置新的日程对象的提醒

~~~kotlin
//...其他代码
// 更新事件到数据库
eventDao.updateEvent(updatedEvent)

// 取消原有的提醒
val alarmReminderManager = AlarmReminderManager(requireContext())
alarmReminderManager.cancelReminder(requireContext(), event.id)
// 设置新的提醒
if (reminderTime > 0L){
    alarmReminderManager.setReminder(updatedEvent)
}
//...其他代码
~~~

* 在DayViewFragment中的deleteEvent()方法下，取消提醒

~~~kotlin
//...其他代码
// 从数据库中删除事件
eventDao.deleteEvent(event)

// 取消提醒
val alarmReminderManager = AlarmReminderManager(requireContext())
alarmReminderManager.cancelReminder(requireContext(), event.id)
//...其他代码
~~~

11. 创建权限管理类 PermissionManager.kt 来统一管理需要的权限，并引导用户授权

* 在 domain/manager 下创建权限管理类
~~~kotlin
/**
 * 权限管理器
 * 负责检查、请求和管理应用程序所需的各种权限
 */
class PermissionManager(private val context: Context) {

    companion object{
        private const val TAG = "PermissionManager"
        const val PERMISSION_GRANTED = 0 // 权限已授予
        const val PERMISSION_REQUEST_NEEDED = 1 // 需要请求权限
        const val PERMISSION_DENIED_PERMANENTLY = 2 // 权限被永久拒绝
    }

    /**
     * 检查通知权限状态
     * @return 权限状态 (PERMISSION_GRANTED, PERMISSION_REQUEST_NEEDED, PERMISSION_DENIED_PERMANENTLY)
     */
    fun checkNotificationPermission(): Int{
        return if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.TIRAMISU){
            when {
                ContextCompat.checkSelfPermission(
                    context,
                    Manifest.permission.POST_NOTIFICATIONS
                ) == PackageManager.PERMISSION_GRANTED -> {
                    PERMISSION_GRANTED
                }else -> {
                    PERMISSION_REQUEST_NEEDED
                }
            }
        } else {
            PERMISSION_GRANTED
        }
    }

    /**
     * 检查浮窗权限状态
     * @return true 如果已授予权限，否则 false
     */
    fun checkOverlayPermission(): Boolean{
        return if(Build.VERSION.SDK_INT >= Build.VERSION_CODES.M){
            Settings.canDrawOverlays(context)
        }else{
            true
        }
    }

    /**
     * 检查精确闹钟权限状态
     * @return true 如果已授予权限，否则 false
     */
    fun checkExactAlarmPermission(): Boolean{
        return if(Build.VERSION.SDK_INT >= Build.VERSION_CODES.S){
            val alarmManager = context.getSystemService(Context.ALARM_SERVICE) as AlarmManager
            alarmManager.canScheduleExactAlarms()
        }else{
            true
        }
    }

    /**
     * 请求通知权限
     * @param launcher 用于启动权限请求的 ActivityResultLauncher
     */
    fun requestNotificationPermission(launcher: ActivityResultLauncher<Intent>) {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.TIRAMISU) {
            val intent = Intent(Settings.ACTION_APPLICATION_DETAILS_SETTINGS).apply {
                data = Uri.fromParts("package", context.packageName, null)
            }
            launcher.launch(intent)
        }
    }

    /**
     * 请求浮窗权限
     * @param launcher 用于启动权限请求的 ActivityResultLauncher
     */
    fun requestOverlayPermission(launcher: ActivityResultLauncher<Intent>) {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
            val intent = Intent(
                Settings.ACTION_MANAGE_OVERLAY_PERMISSION,
                Uri.parse("package:${context.packageName}")
            )
            launcher.launch(intent)
        }
    }

    /**
     * 请求精确闹钟权限
     * @param launcher 用于启动权限请求的 ActivityResultLauncher
     */
    fun requestExactAlarmPermission(launcher: ActivityResultLauncher<Intent>) {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {
            val intent = Intent().apply {
                action = Settings.ACTION_REQUEST_SCHEDULE_EXACT_ALARM
            }
            launcher.launch(intent)
        }
    }

    /**
     * 检查所有必需权限
     * @return true 如果所有权限都已授予，否则 false
     */
    fun checkAllRequiredPermissions(): Boolean {
        return checkOverlayPermission() &&
                checkExactAlarmPermission() &&
                checkNotificationPermission() == PERMISSION_GRANTED
    }

    /**
     * 获取缺失权限的描述信息
     * @return 包含缺失权限信息的列表
     */
    fun getMissingPermissionsDescription(): List<String> {
        val missingPermissions = mutableListOf<String>()

        if (!checkOverlayPermission()) {
            missingPermissions.add("浮窗权限")
        }

        if (!checkExactAlarmPermission()) {
            missingPermissions.add("精确闹钟权限")
        }

        if (checkNotificationPermission() != PERMISSION_GRANTED) {
            missingPermissions.add("通知权限")
        }

        return missingPermissions
    }
}
~~~
* 管理类包含**检查权限**的方法，和引导用户授权的**请求**方法
* 通过设置**Intent**跳转到系统设置页面

---

* 在 MainActivity 类内声明权限管理类

~~~kotlin
private lateinit var permissionManager: PermissionManager
~~~

* 在 onCreate() 方法中初始化权限管理类

~~~kotlin
permissionManager = PermissionManager(this)
~~~

* 修改 checkAndRequestPermissions 方法

~~~kotlin
private fun checkAndRequestPermissions(){
    // 检查精确闹钟权限
    if (!permissionManager.checkExactAlarmPermission()) {
        permissionManager.requestExactAlarmPermission(requestAlarmPermissionLauncher)
    }

    // 检查开启悬浮窗权限
    if (!permissionManager.checkOverlayPermission()) {
        permissionManager.requestOverlayPermission(requestOverlayPermissionLauncher)
    }

    // 检查通知权限
    if (permissionManager.checkNotificationPermission() != PermissionManager.PERMISSION_GRANTED) {
        // 可以选择请求通知权限
        permissionManager.requestNotificationPermission(requestNotificationPermissionLauncher)
    }
}
~~~
* 调用 PermissionManager 类的 **权限检查方法** 若缺少权限则调用对应的**请求方法**
---

* 改进 ReminderReceiver 类中 showFloatingNotification() 方法中的浮窗权限检查逻辑

~~~kotlin
// 检查浮框权限
if (!permissionManager.checkOverlayPermission()) {
    if (!Settings.canDrawOverlays(context)) {
        // 没有权限，尝试发送标准通知作为替代方案
        // showStandardNotification(context, eventTitle, eventId)
        Toast.makeText(context, "请授予悬浮窗权限以显示浮窗提醒", Toast.LENGTH_LONG).show()
        return
    }
}
~~~

* 在 MainActivity 类中添加ActivityResultLauncher

~~~kotlin
// 添加这些ActivityResultLauncher
private val requestAlarmPermissionLauncher = registerForActivityResult(
    ActivityResultContracts.StartActivityForResult()
) { result ->
    // 处理闹钟权限请求结果
    if (permissionManager.checkExactAlarmPermission()) {
        Toast.makeText(this, "精确闹钟权限已授予", Toast.LENGTH_SHORT).show()
    } else {
        Toast.makeText(this, "精确闹钟权限被拒绝", Toast.LENGTH_SHORT).show()
    }
    Log.d("权限请求", "闹钟权限请求结果: ${result.resultCode}")
}

private val requestOverlayPermissionLauncher = registerForActivityResult(
    ActivityResultContracts.StartActivityForResult()
) { result ->
    // 处理悬浮窗权限请求结果
    if (permissionManager.checkOverlayPermission()) {
        Toast.makeText(this, "悬浮窗权限已授予", Toast.LENGTH_SHORT).show()
    } else {
        Toast.makeText(this, "悬浮窗权限被拒绝，请手动开启", Toast.LENGTH_LONG).show()
    }
    Log.d("权限请求", "悬浮窗权限请求结果")
}

private val requestNotificationPermissionLauncher = registerForActivityResult(
    ActivityResultContracts.StartActivityForResult()
) { result ->
    // 处理通知权限请求结果
    when (permissionManager.checkNotificationPermission()) {
        PermissionManager.PERMISSION_GRANTED -> {
            Toast.makeText(this, "通知权限已授予", Toast.LENGTH_SHORT).show()
        }
        PermissionManager.PERMISSION_DENIED_PERMANENTLY -> {
            Toast.makeText(this, "通知权限被永久拒绝，请在设置中手动开启", Toast.LENGTH_LONG).show()
        }
        else -> {
            Toast.makeText(this, "通知权限被拒绝", Toast.LENGTH_SHORT).show()
        }
    }
    Log.d("权限请求", "通知权限请求结果")
}
~~~
* ActivityResultLauncher 用于启动其他Activity，并接收其返回结果，这里是用于处理**权限请求结果**
* 流程：通过registerForActivityResult()方法**注册**ActivityResultLauncher，然后**传入PermissionManager**的请求方法中调用launch()方法**启动**Activity，最后接收其返回结果并**处理结果**
---
至此，我们能够在日程设定的提醒时间中弹出浮窗，并且播放提醒铃声和振动

### 日程排序展示

1. 在eventDao.kt中添加一个查询方法，按照提醒时间升序排序，并且未设置提醒时间的日程排在最后面

~~~kotlin
@Query("SELECT * FROM calendar_events WHERE startTime BETWEEN :start And :end ORDER BY CASE WHEN reminderTime = 0 THEN 1 ELSE 0 END, reminderTime ASC")
suspend fun getEventsInRangeOrderByReminder(start: Long, end: Long): List<CalendarEvent>
~~~

2. 在 DayViewFragment 类中，修改 loadEventFromDatabase() 方法中的查询逻辑

~~~kotlin
val events = eventDao.getEventsInRangeOrderByReminder(startTimeStamp, endTimeStamp)
~~~

* 当然，你也可以按照你喜欢的方式进行排序，只需要修改查询语句即可

### 日视图日期切换

1. 在 DayViewFragment.kt 类中添加一个变量来存储当前选择的日期

~~~kotlin
private var selectedDate: LocalDate = LocalDate.now()
~~~

2. 修改 DayViewFragment.kt 类中的 setupDayView 方法，添加点击监听器

~~~kotlin
// 添加点击监听器以打开日期选择器
binding.dayHeaderText.setOnClickListener {
    showDatePickerDialog()
}
~~~

3. 实现展示日期选择器的方法 showDatePickerDialog() 的逻辑

~~~kotlin
private fun showDatePickerDialog() {
    val datePicker = DatePickerDialog(
        requireContext(),
        { _, year, month, dayOfMonth ->
            // 更新选定日期
            selectedDate = LocalDate.of(year, month + 1, dayOfMonth)

            // 更新UI显示
            binding.dayHeaderText.text = DateTimeFormatter.ofPattern("yyyy年MM月dd日").format(selectedDate)

            // 重新加载事件数据
            loadEventsForDate()
        },
        selectedDate.year,
        selectedDate.monthValue - 1,
        selectedDate.dayOfMonth
    )
    datePicker.show()
}
~~~

4. 修改 loadEventFromDatabase() 方法，传入选择日期参数，从获取当天日期事件转换为选择的日期事件

~~~kotlin
/**
 * 从数据库加载事件数据
 * 异步查询数据库获取当天的所有事件，并更新UI显示
 * - 修改loadEventFromDatabase方法,以接收日期参数
 * - 更改为计算选定日期的开始和结束
 */
private fun loadEventFromDatabase(date: LocalDate){
    lifecycleScope.launch {
        try {
            // 获取数据库实例
            val database = CalendarDatabase.getInstance(requireContext())
            val eventDao = database.eventDao()

            // 更改为计算选定日期的开始和结束
            val startOfDay = date.atStartOfDay()
            val endOfDay = date.atTime(23, 59, 59)

            val startTimeStamp = startOfDay.toInstant(ZoneOffset.UTC).toEpochMilli()
            val endTimeStamp = endOfDay.toInstant(ZoneOffset.UTC).toEpochMilli()

            // 查询数据库并获取事件
            val events = eventDao.getEventsInRangeOrderByReminder(startTimeStamp, endTimeStamp)

            // 更新UI
            updateEventList(events)
        } catch (e: Exception){
            // 处理异常
            Toast.makeText(context, "加载事件失败：${e.message}", Toast.LENGTH_SHORT).show()
        }
    }
}
~~~

### 日程的导出

该步骤要实现将当天的日程导出为**json数据文件**，和**png图片文件**，并保存到手机中

1. 创建一个图标资源文件 ic_export.xml 作为导出按钮的图标

~~~xml
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="24dp"
    android:height="24dp"
    android:viewportWidth="24"
    android:viewportHeight="24"
    android:tint="?attr/colorOnSurface">
    <path
        android:fillColor="@android:color/white"
        android:pathData="M19,12v7L5,19v-7L3,12v7c0,1.1 0.9,2 2,2h14c1.1,0 2,-0.9 2,-2v-7h-2zM13,12.67l2.59,-2.58L17,11.5l-5,5 -5,-5 1.41,-1.41L11,12.67L11,3h2z"/>
</vector>
~~~

2. 修改日视图 fragment_day_view.xml 文件，增加一个横向线性布局，将日期标题和导出按钮添加到该布局中

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<!--
    日视图 Fragment 的布局文件
    包含标题和日视图内容区域
-->
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:orientation="vertical">

    <!-- 新增日视图标题栏容器 -->
    <LinearLayout
        android:id="@+id/dayHeaderContainer"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:gravity="center_vertical"
        android:padding="16dp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent">

        <!-- 日视图标题文本 -->
        <TextView
            android:id="@+id/dayHeaderText"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="日视图"
            android:textAlignment="center"
            android:textAppearance="@style/TextAppearance.Material3.HeadlineSmall" />

        <!-- 导出按钮 -->
        <ImageButton
            android:id="@+id/exportButton"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:background="?attr/selectableItemBackgroundBorderless"
            android:src="@drawable/ic_export"
            android:contentDescription="导出"
            android:padding="8dp"/>
    </LinearLayout>

    <!-- 无事件提示
     - 修改layout_constraintTop_toBottomOf为dayHeaderContainer-->
    <TextView
        android:id="@+id/noEventText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="32dp"
        android:text="今天暂无事件"
        android:textAppearance="@style/TextAppearance.Material3.BodyLarge"
        android:textColor="?attr/colorOnSurfaceVariant"
        android:visibility="gone"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/dayHeaderContainer"/>

    <!-- 事件列表
     - 修改layout_constraintTop_toBottomOf为dayHeaderContainer-->
    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/eventRecyclerView"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:layout_marginTop="16dp"
        android:clipToPadding="false"
        android:paddingBottom="16dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/dayHeaderContainer"/>
</androidx.constraintlayout.widget.ConstraintLayout>
~~~

3. 在 DayViewFragment.kt 类内声明导出按钮，并在 onCreateView 中初始化导出按钮

* 声明导出按钮
~~~kotlin
private lateinit var exportButton: ImageButton
~~~

* 初始化导出按钮
~~~kotlin
exportButton = binding.exportButton // 初始化导出按钮
~~~

4. 创建menu资源 menu_export.xml ，作为点击导出按钮的菜单

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:id="@+id/action_export_data"
        android:title="导出为数据文件" />

    <item
        android:id="@+id/action_export_image"
        android:title="导出为图片" />

    <item
        android:id="@+id/action_import_data_as_today"
        android:title="导入数据为当天日程"/>
</menu>
~~~

5. 在 DayViewFragment.kt 中的 onViewCreated 方法中，为导出按钮添加点击事件监听器

~~~kotlin
// 添加导出按钮点击监听器
exportButton.setOnClickListener {
    showExportMenu(it)
}
~~~

6. 实现导出按钮点击后显示菜单的逻辑，即showExportMenu() 方法

~~~kotlin
private fun showExportMenu(anchorView: View) {
    val popupMenu = PopupMenu(requireContext(), anchorView)
    val menuInflater: MenuInflater = popupMenu.menuInflater
    menuInflater.inflate(R.menu.menu_export, popupMenu.menu)

    popupMenu.setOnMenuItemClickListener { menuItem ->
        when(menuItem.itemId){
            R.id.action_export_data -> {
                exportAsDataFile()
                true
            }
            R.id.action_export_image -> {
                exportAsImage()
                true
            }
            R.id.action_import_data_as_today -> {
                importDataFileAsToday()
                true
            }
            else -> false
        }
    }

    popupMenu.show()
}
~~~
* PopupMenu，即弹出菜单，总是相对于**锚点视图**出现在锚点视图的**底部**或**旁边**
* PopupMenu支持模态显示，当 PopupMenu 显示时，用户需要**先与其交互**才能继续操作应用其他部分
* 这里在导出按钮监听器中传入的 it ，即以导出按钮自身作为锚点视图
* 使用 **manuInflater.inflate()** 方法将 menu_export.xml 填充到 popupMenu 中

7. 在domain/manager中创建导出管理类ExportManager.kt 实现导出数据的获取和图片的绘制

* 创建导出管理类ExportManager.kt
~~~kotlin
class ExportManager(private val context: Context) {
    companion object{
        private const val TAG = "ExportingManager"
    }
}
~~~

* 获取导出数据
~~~kotlin
/**
 * 获取指定日期的事件数据并转换为JSON格式
 * @param date 指定日期
 * @param scope 协程作用域
 * @param onSuccess 成功回调，返回JSON格式的字节数组
 * @param onError 失败回调
 */
fun getEventsDataAsJson(
    date: LocalDate,
    scope: CoroutineScope,
    onSuccess: (ByteArray) -> Unit,
    onError: (String) -> Unit
) {
    scope.launch {
        try {
            // 获取数据库实例
            val database = CalendarDatabase.getInstance(context)
            val eventDao = database.eventDao()

            // 计算指定日期开始和结束时间戳
            val startOfDay = date.atStartOfDay()
            val endOfDay = date.atTime(23, 59, 59)

            val startTimestamp = startOfDay.toInstant(ZoneOffset.UTC).toEpochMilli()
            val endTimestamp = endOfDay.toInstant(ZoneOffset.UTC).toEpochMilli()

            // 查询该日期的事件
            val events = eventDao.getEventsInRangeOrderByReminder(startTimestamp, endTimestamp)

            // 转换为JSON格式
            val gson = Gson()
            val jsonString = gson.toJson(events)
            val byteArray = jsonString.toByteArray(Charsets.UTF_8)

            onSuccess(byteArray)
        } catch (e: Exception) {
            Log.e(TAG, "获取事件数据失败：${e.message}")
            onError(e.message ?: "未知错误")
        }
    }
}
~~~

* 引入Gson依赖
~~~gradle
implementation("com.google.code.gson:gson:2.10.1")
~~~

* 绘制图片
~~~kotlin
/**
 * 创建指定视图的截图
 * @param view 要截图的视图
 * @return 截图的Bitmap对象
 */
fun takeScreenshot(recyclerView: RecyclerView): Bitmap {
    // 启用绘制缓存
    recyclerView.measure(
        View.MeasureSpec.makeMeasureSpec(recyclerView.width, View.MeasureSpec.EXACTLY),
        View.MeasureSpec.makeMeasureSpec(0, View.MeasureSpec.UNSPECIFIED)
    )

    recyclerView.layout(0, 0, recyclerView.measuredWidth, recyclerView.measuredHeight)

    // 创建足够大的Bitmap来容纳所有内容
    val bitmap = createBitmap(recyclerView.width, recyclerView.measuredHeight)
    val canvas = Canvas(bitmap)
    recyclerView.draw(canvas)
    return bitmap
}
~~~
* 启用**绘制缓存**，捕获视图某一时刻的快照，避免性能损失
* 宽度采用EXACTLY；而高度采用UNSPECIFIED，这样图片会**自动适应**视图的高度，可以显示超出屏幕显示的部分
* 使用 createBitmap() 方法创建一个Bitmap对象，并传入视图的宽度和高度
* 使用 Canvas 对象**绑定**bitmap，调用draw() 方法，将recyclerView**绘制**在canvas上

---

* 将bitmap转换为字节数组

~~~kotlin
/**
 * 将Bitmap转换为PNG格式的字节数组
 * @param bitmap 要转换的Bitmap
 * @return PNG格式的字节数组
 */
fun convertBitmapToPngByteArray(bitmap: Bitmap): ByteArray {
    val outputStream = ByteArrayOutputStream()
    bitmap.compress(Bitmap.CompressFormat.PNG, 100, outputStream)
    return outputStream.toByteArray()
}
~~~
* compress() 方法指定**压缩格式**，**压缩质量**和**输出流**
* 这里指定输出格式为 PNG，质量为 100，输出流为 outputStream(内存输出流)

8. 在 DayViewFragment 类内声明导出管理类，并在onCreateView中初始化

* 声明
~~~kotlin
private lateinit var exportManager: ExportManager
~~~

* 初始化
~~~kotlin
exportManager = ExportManager(requireContext())
~~~
* 在 Fragment 中，使用上下文都要指定为 **requireContext()**
* Fragment不像Activity那样直接继承Context，它需要**通过Activity来获取Context**
* 在Fragment的生命周期中，Context的可用性是**变化的**

9. 实现showExportMenu()中的导出功能

* 导出为数据文件 exportAsDataFile() 方法
~~~kotlin
private fun exportAsDataFile() {
    val intent = Intent(Intent.ACTION_CREATE_DOCUMENT).apply {
        addCategory(Intent.CATEGORY_OPENABLE)
        type = "application/json"
        putExtra(Intent.EXTRA_TITLE, "calendar_events_${selectedDate}.json")
    }
    createDataFileLauncher.launch(intent)
}
~~~
* 调用 ACTION_CREATE_DOCUMENT 来打开系统文件选择器，让用户选择保存文件的**位置和名称**
* `addCategory()` 方法指定CATEGORY_OPENABLE，即Intent的配置类型是**可打开的**
* `type` 指定文件类型为JSON格式，文件选择器会根据MIME类型**过滤显示**的文件，系统会为创建的文件**提供合适的默认扩展名**，确保创建的文件类型与内容**匹配**
* `putExtra(Intent.EXTRA_TITLE, "文件名")` 指定了**默认的文件名**

---
* 导出为图片文件 exportAsImageFile() 方法

~~~kotlin
private fun exportAsImage() {
    val intent = Intent(Intent.ACTION_CREATE_DOCUMENT).apply {
        addCategory(Intent.CATEGORY_OPENABLE)
        type = "image/png"
        putExtra(Intent.EXTRA_TITLE, "calendar_screenshot_${selectedDate}.png")
    }
    createImageFileLauncher.launch(intent)
}
~~~

10. 分别完成 *createDataFileLauncher* 和 *createImageFileLauncher* 的实现，用于执行**启动文件创建流程**以及处理文件创建**结果的回调**

* createDataFileLauncher 实现

~~~kotlin
private val createDataFileLauncher = registerForActivityResult(
    ActivityResultContracts.StartActivityForResult()
) { result ->
    if (result.resultCode == Activity.RESULT_OK) {
        val uri = result.data?.data
        if (uri != null) {
            // 获取事件数据并保存到文件
            exportManager.getEventsDataAsJson(
                selectedDate,
                lifecycleScope,
                onSuccess = { data ->
                    try {
                        requireContext().contentResolver.openOutputStream(uri)?.use { outputStream ->
                            outputStream.write(data)
                        }
                        Toast.makeText(requireContext(), "事件数据已保存", Toast.LENGTH_SHORT).show()
                    } catch (e: Exception) {
                        Toast.makeText(requireContext(), "保存失败：${e.message}", Toast.LENGTH_SHORT).show()
                    }
                },
                onError = { error ->
                    Toast.makeText(requireContext(), "导出失败：$error", Toast.LENGTH_SHORT).show()
                }
            )
        }
    }
}
~~~
* registerForActivityResult是Activity Result API的核心部分，用于**注册**一个ActivityResultLauncher，以便**启动**其他Activity并**处理返回的结果**
* ActivityResultLauncher是Android Activity Result API中的一个核心组件，它是一个泛型类，用于**启动**其他Activity并**处理返回的结果**
* 在这里启动的是launch传入的Intent
* ActivityResultContracts是Android Activity Result API中的一个重要组成部分，它定义了Activity之间交互的契约，包括**输入参数类型**和**输出结果类型**
* StartActivityForResult是ActivityResultContracts中的一种预定义契约；**输入类型：Intent** - 用于启动目标Activity的意图；**输出类型：ActivityResult** - 包含结果码和返回数据的封装对象
* 通过result.data?.data获取**文件uri**，并在成功回调方法中使用contentResolver打开**文件输出流**，并通过uri写入getEventsDataAsJson()方法返回的数据
---
* createImageFileLauncher 实现
~~~kotlin
private val createImageFileLauncher = registerForActivityResult(
    ActivityResultContracts.StartActivityForResult()
) { result ->
    if (result.resultCode == Activity.RESULT_OK) {
        val uri = result.data?.data
        if (uri != null) {
            // 创建截图并保存到文件
            val bitmap = exportManager.takeScreenshot(binding.eventRecyclerView)
            val imageData = exportManager.convertBitmapToPngByteArray(bitmap)

            try {
                requireContext().contentResolver.openOutputStream(uri)?.use { outputStream ->
                    outputStream.write(imageData)
                }
                Toast.makeText(requireContext(), "截图已保存", Toast.LENGTH_SHORT).show()
            } catch (e: Exception) {
                Toast.makeText(requireContext(), "保存失败：${e.message}", Toast.LENGTH_SHORT).show()
            }
        }
    }
}
~~~

至此，**导出功能**实现完毕

### 日程的导入

1. 创建事件导入管理类 ImportManager.kt 来实现导入事件的解析和数据库操作

* 数据文件导入方法 importEventsFromJson() 和 importEventsToOriginalDates()
~~~kotlin
// 导入日程到当前日期
fun importEventsFromJson(
    uri: Uri,
    targetDate: LocalDate,
    scope: CoroutineScope,
    onSuccess: () -> Unit,
    onError: (String) -> Unit
){
    scope.launch {
        try {
            // 读取文件内容
            val inputStream = withContext(Dispatchers.IO){
                context.contentResolver.openInputStream(uri)
            }

            if(inputStream == null){
                onError("无法打开文件")
                return@launch
            }

            // 解析Json数据
            val events = parseJsonEvents(inputStream)

            // 调整事件日期并保存到数据库
            saveEventToDatabase(events, targetDate)

            onSuccess()
        }catch (e: Exception){
            Log.e(TAG, "导入事件失败: ${e.message}")
            onError("导入事件失败: ${e.message}")
        }
    }
}

// 导入日程到数据文件中原有的日期
fun importEventsToOriginalDates(
    uri: Uri,
    scope: CoroutineScope,
    onSuccess: () -> Unit,
    onError: (String) -> Unit
){
    scope.launch {
        try{
            // 读取文件内容
            val inputStream = withContext(Dispatchers.IO) {
                context.contentResolver.openInputStream(uri)
            }

            if (inputStream == null) {
                onError("无法打开文件")
                return@launch
            }

            // 解析Json数据
            val events = parseJsonEvents(inputStream)

            // 保存事件到其原始日期
            saveEventToOriginalDates(events)

            onSuccess()
        }catch (e: Exception){
            Log.e(TAG, "导入事件失败: ${e.message}")
            onError("导入事件失败: ${e.message}")
        }
    }
}
~~~
* 通过withContext(Dispatchers.IO)将读取文件内容放在**IO线程**中执行

---

* Json数据解析方法 parseJsonEvents()

~~~kotlin
private suspend fun parseJsonEvents(inputStream: InputStream): List<CalendarEvent>{
    return withContext(Dispatchers.IO){
        try{
            val jsonString = inputStream.bufferedReader().use { it.readText() }
            val gson = Gson()
            val listType = object : TypeToken<List<CalendarEvent>>() {}.type
            gson.fromJson(jsonString, listType) as List<CalendarEvent>
        } finally {
            inputStream.close()
        }
    }
}
~~~

* 数据库操作方法 saveEventToDatabase() 和 saveEventToOriginalDates()

~~~kotlin
// 保存事件到原有日期
private suspend fun CoroutineScope.saveEventToOriginalDates(events: List<CalendarEvent>) {
    withContext(Dispatchers.IO){
        val database = CalendarDatabase.getInstance(context)
        val eventDao = database.eventDao()

        events.forEach { event ->
            // 检查事件是否已经存在
            val existingEvents = eventDao.getEventsInRange(event.startTime, event.endTime)

            // 检查是否已经存在相同标题和时间的事件
            val isDuplicate = existingEvents.any { existingEvent ->
                existingEvent.title == event.title &&
                        existingEvent.startTime == event.startTime &&
                        existingEvent.endTime == event.endTime
            }

            // 不存在重复事件，则插入新事件
            if(!isDuplicate){
                // 创建新的事件对象，保持原始日期和时间
                val newEvent = event.copy(
                    id = 0,
                    title = event.title,
                    content = event.content,
                    startTime = event.startTime,
                    endTime = event.endTime,
                    eventColor = eventColorManager.getRandomColor(),
                    reminderTime = event.reminderTime,
                    isCompleted = event.isCompleted
                )

                // 插入到数据库
                eventDao.insertEvent(newEvent)
            }
        }


    }
}

private suspend fun CoroutineScope.saveEventToDatabase(
    events: List<CalendarEvent>,
    targetDate: LocalDate
) {
    withContext(Dispatchers.IO){
        val database = CalendarDatabase.getInstance(context)
        val eventDao = database.eventDao()

        // 计算目标日期的时间戳范围
        val targetStartOfDay = targetDate.atStartOfDay()
        val targetEndOfDay = targetDate.atTime(23, 59, 59)

        val targetStartTimestamp = targetStartOfDay.toInstant(ZoneOffset.UTC).toEpochMilli()
        val targetEndTimestamp = targetEndOfDay.toInstant(ZoneOffset.UTC).toEpochMilli()

        // 保存调整后的事件
        events.forEach { event ->
            // 保持原始事件的时长
            val duration = event.endTime - event.startTime

            // 创建新的事件对象，日期调整为目标日期，但保持原始的时间和时长
            val newEvent = event.copy(
                id = 0, // 设置为0，Room会自动生成新的id
                startTime = targetStartTimestamp,
                endTime = targetStartTimestamp + duration,
                title = event.title,
                content = event.content,
                eventColor = eventColorManager.getRandomColor(),
                reminderTime = if (event.reminderTime > 0){
                    targetStartTimestamp + (event.reminderTime - event.startTime)
                }else {
                    0L
                },
                isCompleted = event.isCompleted
            )

            // 插入到数据库
            eventDao.insertEvent(newEvent)
        }
    }
}
~~~
* 插入新事件时，将id设置为**0**，Room会自动生成新的id

2. 修改表单组件menu_export.xml

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:id="@+id/action_export_data"
        android:title="导出为数据文件" />

    <item
        android:id="@+id/action_export_image"
        android:title="导出为图片" />

    <item
        android:id="@+id/action_import_data_as_today"
        android:title="导入数据为当天日程"/>

    <item
        android:id="@+id/action_import_data_to_original_dates"
        android:title="导入数据为默认日程"/>
</menu>
~~~

3. 在DayViewFragment中声明并初始化ImportManager

* 类内声明ImportManager
~~~kotlin
private lateinit var importManager: ImportManager
~~~

* 在onViewCreated()方法中初始化ImportManager
~~~kotlin
// 初始化导入管理类
importManager = ImportManager(requireContext())
~~~



4. 在DayViewFragment的showExportMenu()方法中添加导入功能

~~~kotlin
R.id.action_import_data_as_today -> {
    importDataFileAsToday()
    true
}
R.id.action_import_data_to_original_dates -> {
    importDataFileToOriginalDates()
    true
}
~~~

5. 实现importDataFileAsToday()和importDataFileToOriginalDates()方法

~~~kotlin
private fun importDataFileToOriginalDates() {
    val intent = Intent(Intent.ACTION_GET_CONTENT).apply {
        type = "*/*"
        addCategory(Intent.CATEGORY_OPENABLE)
    }
    importDataToOriginalDatesLauncher.launch(intent)
}

private fun importDataFileAsToday() {
    val intent = Intent(Intent.ACTION_GET_CONTENT).apply {
        type = "*/*"
        addCategory(Intent.CATEGORY_OPENABLE)
    }

    importDataLauncher.launch(intent)
}
~~~

6. 实现importDataLauncher和importDataToOriginalDatesLauncher

~~~kotlin
private val importDataToOriginalDatesLauncher = registerForActivityResult(
    ActivityResultContracts.StartActivityForResult()
){ result ->
    if (result.resultCode == Activity.RESULT_OK){
        val uri = result.data?.data
        // 导入事件数据到原始日期
        if (uri != null){
            importManager.importEventsToOriginalDates(
                uri,
                viewLifecycleOwner.lifecycleScope,
                onSuccess = {
                    // 导入成功，刷新事件列表
                    loadEventFromDatabase(selectedDate)
                    Toast.makeText(requireContext(), "导入成功", Toast.LENGTH_SHORT).show()
                },
                onError = { error ->
                    Toast.makeText(requireContext(), "导入失败：$error", Toast.LENGTH_SHORT).show()
                }
            )
        }
    }
}

private val importDataLauncher = registerForActivityResult(
    ActivityResultContracts.StartActivityForResult()
){result ->
    if (result.resultCode == Activity.RESULT_OK){
        val uri = result.data?.data
        if(uri != null){
            // 导入事件数据
            importManager.importEventsFromJson(
                uri,
                selectedDate,
                viewLifecycleOwner.lifecycleScope,
                onSuccess = {
                    // 导入成功，刷新事件列表
                    loadEventFromDatabase(selectedDate)
                    Toast.makeText(requireContext(), "导入成功", Toast.LENGTH_SHORT).show()
                },
                onError = { error ->
                    Toast.makeText(requireContext(), "导入失败：$error", Toast.LENGTH_SHORT).show()
                }
            )
        }
    }
}
~~~

至此，导入功能已经实现。

## 农历实现

1. 引入农历依赖

* libs.versions.toml
~~~toml
[versions]
lunarcalendar = "4.0.0"

[libraries]
lunarcalendar = { module = "com.github.XhinLiang:LunarCalendar", version.ref = "lunarcalendar" }
~~~

* 模块级 build.gradle
~~~kotlin
dependencies {
    implementation(libs.lunarcalendar)
}
~~~

2. 修改 calendar_day_layout.xml

~~~xml
    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:orientation="vertical"
        android:gravity="center">

        <!-- 显示日期数字的文本视图 -->
        <TextView
            android:id="@+id/calendarDayText"
            android:layout_width="42dp"
            android:layout_height="42dp"
            android:gravity="center"
            tools:text="22"
            android:textSize="16sp"
            android:textStyle="bold"/>

        <!-- 显示农历的文本视图 -->
        <TextView
            android:id="@+id/calendarLunarText"
            android:layout_width="42dp"
            android:layout_height="wrap_content"
            android:gravity="center"
            tools:text="初一"
            android:textSize="12sp"
            android:textColor="#808080"
            android:maxLines="1"
            android:ellipsize="end"/>

    </LinearLayout>
~~~

3. 在DayViewContainer和MonthDayViewContainer中添加农历文本

* DayViewContainer.kt
~~~kotlin
    val lunarTextView: TextView = view.findViewById(R.id.calendarLunarText)
~~~

* MonthDayViewContainer.kt
~~~kotlin
    val lunarTextView: TextView = view.findViewById(com.kei.mycalendarapp.R.id.calendarLunarText)
~~~

4. 修改WeekViewBinder.kt

* 在bind()方法中添加农历显示

~~~kotlin
// 设置农历信息
val lunarText = getLunarText(data.date)
container.lunarTextView.text = lunarText
// 设置农历文本样式
container.lunarTextView.textSize = 12f
container.lunarTextView.setTextColor(Color.GRAY)
container.lunarTextView.alpha = 0.8f // 设置半透明效果
~~~

* 实现getLunarText()方法

~~~kotlin
private fun getLunarText(date: LocalDate): String? {
    val lunarCalendar = LunarCalendar.obtainCalendar(
        date.year,
        date.monthValue,
        date.dayOfMonth
    )
    return lunarCalendar.lunarDay
}
~~~

5. 修改 MonthDayViewBinder.kt 

* 在bind()方法中添加农历显示
~~~kotlin
// 设置农历信息
val lunarText = getLunarText(data.date)
container.lunarTextView.text = lunarText
// 设置农历文本样式
container.lunarTextView.textSize = 12f
container.lunarTextView.setTextColor(Color.GRAY)
container.lunarTextView.alpha = 0.8f // 设置半透明效果
~~~

* 实现getLunarText()方法
~~~kotlin
private fun getLunarText(date: LocalDate): String? {
    val lunarCalendar = LunarCalendar.obtainCalendar(
        date.year,
        date.monthValue,
        date.dayOfMonth
    )
    return lunarCalendar.lunarDay
}
~~~

## 网络订阅

[回到目录](#目录)

### 周视图节日

[回到上一级](#网络订阅)

1. 在AndroidManifest中添加网络权限

~~~xml
<!-- 添加网络权限 -->
<uses-permission android:name="android.permission.INTERNET"/>
~~~

2. 创建能容纳一个节日的卡片资源 item_festival.xml

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal"
    android:padding="12dp">

    <ImageView
        android:layout_width="24dp"
        android:layout_height="24dp"
        android:src="@drawable/ic_notification"
        android:layout_marginEnd="12dp" />

    <TextView
        android:id="@+id/festivalText"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:textSize="16sp"
        android:textColor="@android:color/black" />

</LinearLayout>
~~~

3. 创建能容纳节日卡片的容器视图 fragment_today_festival.xml

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="今天的节日"
        android:textSize="18sp"
        android:textStyle="bold"
        android:layout_marginBottom="16dp" />

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/festivalRecyclerView"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</LinearLayout>
~~~

4. 在 fragment_week_view.xml 中添加 tabLayout 和 viewPager 以便容纳节日视图以及后续其他网络模块

~~~xml
    <!-- 模块选择栏 -->
    <com.google.android.material.tabs.TabLayout
        android:id="@+id/moduleTabLayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:tabMode="fixed"
        app:tabGravity="fill"/>

    <!-- 模块内容显示区域 -->
    <androidx.viewpager2.widget.ViewPager2
        android:id="@+id/moduleViewPager"
        android:layout_width="match_parent"
        android:layout_height="200dp"/>
~~~

5. 添加okhttp依赖

* 在 libs.versions.toml 中添加

~~~toml
[versions]
okhttp = "5.3.0"

[libraries]
okhttp = { module = "com.squareup.okhttp3:okhttp", version.ref = "okhttp" }
~~~

* 在模块级 build.gradle 中添加

~~~kotlin
dependencies {
    implementation(libs.okhttp)
}
~~~

6. 在 domain/utils 下创建网络请求类 HolidayApiService.kt

~~~kotlin
class HolidayApiService {
    private val client = OkHttpClient()
    private val gson = Gson()

    fun getHolidayInfo(date: LocalDate, callback: (HolidayResponse?) -> Unit){
        val formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd")
        val dateString = date.format(formatter)
        val url = "https://timor.tech/api/holiday/info/$dateString"

        val request = Request.Builder()
            .url(url)
            .addHeader("User-Agent", "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36")
            .addHeader("Accept", "text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8")
            .addHeader("Accept-Language", "zh-CN,zh;q=0.9,en;q=0.8")
            .addHeader("Connection", "keep-alive")
            .addHeader("Upgrade-Insecure-Requests", "1")
            .build()

        client.newCall(request).enqueue(object : Callback{
            override fun onFailure(call: Call, e: IOException) {
                callback(null)
                Log.e("HolidayApiService", "网络请求失败${e.message}", e)
            }

            override fun onResponse(call: Call, response: Response) {
                response.use {
                    if (!response.isSuccessful){
                        Log.d("HolidayApiService", "获取的节假日信息${response.body.string()}")
                        callback(null)
                        return
                    }

                    val jsonData = response.body.string()
                    try{
                        val holidayResponse = gson.fromJson(jsonData, HolidayResponse::class.java)
                        Log.d("HolidayApiService", "获取的节假日信息${holidayResponse}")
                        callback(holidayResponse)
                    }catch (e: Exception){
                        Log.d("HolidayApiService", "获取的节假日信息${jsonData}")
                        Log.e("HolidayApiService", "解析JSON数据失败${e.message}", e)
                    }
                }
            }
        })
    }
}
~~~
* 请求需要添加请求头，以模拟浏览器请求

7. 在 presentation/viewmodel 下创建 SharedViewModel.kt 类，用于存放 LiveData 属性的变量，以实现某些变量改变时的监听，比如选择日期的变化

~~~kotlin
class SharedViewModel : ViewModel() {
    private val _selectedDate = MutableLiveData<LocalDate>()
    val selectedDate: LiveData<LocalDate> = _selectedDate

    fun setSelectedDate(date: LocalDate) {
        _selectedDate.value = date
    }
}
~~~

8. 在presentation/ui/adapter 下创建节日适配器 FestivalAdapter.kt 类，用于显示节日列表，获取数据并更新列表

~~~kotlin
class FestivalAdapter(private var festivals: List<String>) : RecyclerView.Adapter<FestivalAdapter.FestivalViewHolder> () {
    inner class FestivalViewHolder(view: View): RecyclerView.ViewHolder(view){
        val festivalText: TextView = view.findViewById(R.id.festivalText)
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): FestivalViewHolder {
        val view = LayoutInflater.from(parent.context)
            .inflate(R.layout.item_festival, parent, false)
        return FestivalViewHolder(view)
    }

    override fun onBindViewHolder(holder: FestivalViewHolder, position: Int) {
        holder.festivalText.text = festivals[position]
    }

    override fun getItemCount(): Int {
        return festivals.size
    }

    fun updateFestivals(newFestivals: List<String>){
        festivals = newFestivals
        notifyDataSetChanged()
    }
}
~~~

9. 在 presentation/ui/adapter 下创建
ModulePagerAdapter.kt 类，用于管理模块的页面

~~~kotlin
/**
 * 模块化ViewPager适配器
 *
 * @param fragmentActivity FragmentActivity
 * @param currentDate 当前日期
 */
class ModulePagerAdapter(fragmentActivity: FragmentActivity, private var currentDate: LocalDate = LocalDate.now()) : FragmentStateAdapter(fragmentActivity){

    /**
     * 获取模块数量
     *
     * @return 模块数量
     */
    override fun getItemCount(): Int = 1

    /**
     * 创建指定位置的Fragment
     *
     * @param position 位置
     * @return 创建的Fragment
     */
    override fun createFragment(position: Int): Fragment {
        return when(position){
            0 -> TodayFestivalFragment.newInstance(currentDate)
            else -> TodayFestivalFragment.newInstance(currentDate)
        }
    }
}
~~~

10. 在 presentation/ui/fragment 下创建 TodayFestivalFragment.kt 类，用于显示特定日期的节日列表

~~~kotlin
class TodayFestivalFragment: Fragment() {

    companion object{
        private const val ARG_DATE = "arg_date"

        fun newInstance(date: LocalDate): TodayFestivalFragment{
            val fragment = TodayFestivalFragment()
            val args = Bundle()
            args.putSerializable(ARG_DATE, date.toString())
            fragment.arguments = args
            return fragment
        }
    }

    private lateinit var holidayApiService: HolidayApiService
    private lateinit var recyclerView: RecyclerView
    private lateinit var sharedViewModel: SharedViewModel
    private var festivalAdapter: FestivalAdapter? = null
    private var currentDate: LocalDate? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        sharedViewModel = ViewModelProvider(requireActivity())[SharedViewModel::class.java]

        arguments?.getString(ARG_DATE)?.let {
            currentDate = LocalDate.parse(it)
        } ?: run {
            currentDate = LocalDate.now()
        }
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        sharedViewModel.selectedDate.observe(viewLifecycleOwner){date ->
            updateFestivalsForDate(date)
        }
    }

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        val view = inflater.inflate(R.layout.fragment_today_festival, container, false)
        recyclerView = view.findViewById(R.id.festivalRecyclerView)
        recyclerView.layoutManager = LinearLayoutManager(context)
        festivalAdapter = FestivalAdapter(emptyList())
        recyclerView.adapter = festivalAdapter
        holidayApiService = HolidayApiService()

        currentDate?.let { updateFestivalsForDate(it) } ?:updateFestivalsForDate(LocalDate.now())

        return view
    }

    fun updateFestivalsForDate(date: LocalDate){
        currentDate = date
        holidayApiService.getHolidayInfo(date){ response ->
            activity?.runOnUiThread {
                val festivals = mutableListOf<String>()

                // 添加对空响应的检查
                if (response == null) {
                    // 网络请求失败或API返回空响应
                    festivals.add("网络连接失败，请检查网络设置")
                    festivalAdapter?.updateFestivals(festivals)
                    return@runOnUiThread
                }

                if(response?.holiday != null){
                    // 是节日
                    festivals.add(response.holiday.name)
                }else if (response?.type != null){
                    // 不是节日
                    if (response.type.type == 1){
                        // 周末
                        festivals.add(response.type.name)
                    }else{
                        // 工作日
                        festivals.add("今天是${response.type.name}")
                    }
                }else{
                    festivals.add("无法获取节日信息")
                }

                festivalAdapter?.updateFestivals(festivals)
            }
        }
    }
}
~~~
* 在 updateFestivalsForDate 方法中，调用HolidayApiService 的 getHolidayInfo 方法获取**指定日期**的节日信息
* 在 onViewCreated 方法中，为selectedDate 设置**监听器**，当用户选择新的日期时，会调用 updateFestivalsForDate 方法更新显示的节日信息

11. 在 WeekViewFragment.kt 类内声明相关类并在对应生命周期中初始化

* 声明相关类
~~~kotlin
private lateinit var moduleAdapter: ModulePagerAdapter
private lateinit var moduleTabLayout: TabLayout
private lateinit var moduleViewPager: ViewPager2
private lateinit var sharedViewModel: SharedViewModel
~~~

* 初始化

~~~kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    sharedViewModel = ViewModelProvider(requireActivity())[SharedViewModel::class.java]
}
~~~
* 剩下三个在onViewCreated 方法中初始化

11. 在 WeekViewFragment.kt 中onViewCreated 方法中添加 setupModuleSection 方法，用于初始化模块部分

~~~kotlin
private fun setupModuleSection() {
    moduleTabLayout = binding.root.findViewById(R.id.moduleTabLayout)
    moduleViewPager = binding.root.findViewById(R.id.moduleViewPager)

    // 设置viewPager
    moduleAdapter = ModulePagerAdapter(requireActivity())
    moduleViewPager.adapter = moduleAdapter

    // 关联 TabLayout 和 ViewPager
    TabLayoutMediator(moduleTabLayout, moduleViewPager){ tab, position ->
        when(position){
            0 -> {
                tab.text = "节日"
            }
        }
    }.attach()
}
~~~

12. 在 setupWeekView 的 日期点击监听器中设置sharedViewModel中的selectedDate属性为点击的日期，通知观察者调用网络查询并更新UI

~~~kotlin
// 通过ViewModel更新选中的日期
sharedViewModel.setSelectedDate(weekDay.date)
~~~

### 周视图番组表
[回到上一级](#网络订阅)

#### 第一步：定义数据模型
[回到上一级](#周视图番组表)

在 data/local/entity 下**定义数据模型** BangumiResponse.kt 用于保存网络请求返回的数据，并持久化存储到Room数据库中

~~~kotlin
package com.kei.mycalendarapp.data.local.entity

import com.kizitonwose.calendar.core.WeekDay

data class BangumiResponse(
    val weekday: Weekday,
    val items: List<BangumiItem>
)

data class Weekday(
    val en: String,
    val cn: String,
    val ja: String,
    val id: Int
)

data class BangumiItem(
    val id: Int,
    val url: String,
    val type: Int,
    val name: String,
    val name_cn: String,
    val summary: String,
    val air_date: String,
    val air_weekday: Int,
    val rating: Rating?,
    val images: Images
)

data class Rating(
    val total: Int,
    val count: Map<String, Int>,
    val score: Double
)

data class Images(
    val large: String,
    val common: String,
    val medium: String,
    val small: String,
    val grid: String
)
~~~
* BangumiResponse类：接受当天响应数据列表
* Weekday类：接受星期数据
* BangumiItem类：一部番剧的数据
* Rating类：一部番剧的评分数据
* Images类：一部番剧的图片数据

#### 第二步：网络请求服务
[回到上一级](#周视图番组表)

在 domain/utils 下创建一个网络请求服务类 BangumiApiService.kt ，用于获取BangumiApi数据

~~~kotlin
class BangumiApiService {
    private var client = OkHttpClient()
    private val gson = Gson()
    private val TAG = "BangumiApiService"

    constructor(context: Context){
        client = CacheManager.getInstance(context).okHttpClient
    }

    fun getBangumiCalendar(callback: (List<BangumiResponse>?) -> Unit){
        val url = "https://api.bgm.tv/calendar"

        val request = Request.Builder()
            .url(url)
            .addHeader("User-Agent", "MyCalendarApp/1.0")
            .addHeader("Accept", "application/json")
            .build()

        client.newCall(request).enqueue(object : Callback{
            override fun onFailure(call: Call, e: IOException) {
                Log.e(TAG, "网络请求失败${e.message}", e)
                callback(null)
            }

            override fun onResponse(call: Call, response: Response) {
                try {
                    val body = response.body.string()
                    if (response.isSuccessful){
                        val listType = object : TypeToken<List<BangumiResponse>>() {}.type
                        val bangumiList: List<BangumiResponse> = gson.fromJson(body, listType)
                        callback(bangumiList)
                    }else{
                        Log.e(TAG, "获取番剧日历失败${response.code} ${response.message} ${body}")
                        callback(null)
                    }
                } catch (e: Exception){
                    Log.e(TAG, "解析JSON数据失败${e.message}", e)
                    callback(null)
                }
            }
        })
    }

    fun getBangumiForDate(date: LocalDate, callback: (List<BangumiItem>?) -> Unit){
        getBangumiCalendar { bangumiList ->
            if( bangumiList != null){
                val dayOfWeek = date.dayOfWeek.value
                val dayBangumi = bangumiList.find{ it.weekday.id == dayOfWeek}
                callback(dayBangumi?.items)
            } else{
                callback(null)
            }
        }
    }
}
~~~
* 注意request需要添加请求头，模拟浏览器请求
* 由于Api返回的是标准的Json数据，因此直接使用Gson解析即可
* 利用数组的find方法，找到对应周几的番剧

#### 第三步：UI设计

1. 创建一个 item_anime_card.xml 文件，以卡片的形式展示每一步番剧的信息

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.cardview.widget.CardView xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="8dp"
    app:cardCornerRadius="8dp"
    app:cardElevation="4dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:padding="12dp">

        <ImageView
            android:id="@+id/animeImageView"
            android:layout_width="80dp"
            android:layout_height="100dp"
            android:scaleType="centerCrop"
            android:contentDescription="番剧封面" />

        <LinearLayout
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:layout_marginStart="12dp"
            android:orientation="vertical">

            <TextView
                android:id="@+id/animeTitleTextView"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textSize="16sp"
                android:textStyle="bold"
                android:textColor="@android:color/black"
                android:maxLines="2"
                android:ellipsize="end" />

            <TextView
                android:id="@+id/animeInfoTextView"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginTop="4dp"
                android:textSize="14sp"
                android:textColor="@android:color/darker_gray"
                android:maxLines="3"
                android:ellipsize="end" />

        </LinearLayout>

        <ImageButton
            android:id="@+id/addEventButton"
            android:layout_width="48dp"
            android:layout_height="48dp"
            android:layout_gravity="top"
            android:layout_marginStart="8dp"
            android:background="?attr/selectableItemBackgroundBorderless"
            android:src="@drawable/ic_add_event"
            android:contentDescription="添加到日程" />

    </LinearLayout>

</androidx.cardview.widget.CardView>
~~~

2. 创建 fragment_anime_schedule.xml 文件，并使用 RecyclerView 显示动画日程列表

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical">

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/animeRecyclerView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="8dp" />

</LinearLayout>
~~~

3. 在 presentation/ui/adapter 中创建 AnimeAdapter.kt，用于将列表传入的每一部番剧的**信息**与**卡片视图**中的组件进行绑定

~~~kotlin
class AnimeAdapter(
    private var animeList: List<BangumiItem>,
    private val onAddToCalendarListener: (BangumiItem) -> Unit
): RecyclerView.Adapter<AnimeAdapter.AnimeViewHolder>() {

    inner class AnimeViewHolder(view: View): RecyclerView.ViewHolder(view){
        val animeImageView: ImageView = view.findViewById(R.id.animeImageView)
        val animeTitleTextView: TextView = view.findViewById(R.id.animeTitleTextView)
        val animeInfoTextView: TextView = view.findViewById(R.id.animeInfoTextView)
        val addEventButton: ImageButton = view.findViewById(R.id.addEventButton)
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): AnimeViewHolder {
        val view = LayoutInflater.from(parent.context)
            .inflate(R.layout.item_anime_card, parent, false)
        return AnimeViewHolder(view)
    }

    override fun onBindViewHolder(holder: AnimeViewHolder, position: Int) {
        val anime = animeList[position]

        // 显示番剧标题
        holder.animeTitleTextView.text = anime.name_cn.ifEmpty { anime.name }

        // 显示番剧信息
        val info = "播出日期: ${anime.air_date}\nID: ${anime.id}"
        holder.animeInfoTextView.text = info

        // 加载番剧封面
        Log.d("AnimeAdapter", "Image URL: ${anime.images.medium}")
        Glide.with(holder.animeImageView.context)
            .load(anime.images.medium)
            .placeholder(R.drawable.ic_placeholder)
            .into(holder.animeImageView)

        // 设置添加到日程的点击事件
        holder.addEventButton.setOnClickListener {
            onAddToCalendarListener(anime)
        }
    }

    override fun getItemCount(): Int {
        return animeList.size
    }

    fun updateAnimeList(newList: List<BangumiItem>){
        animeList = newList
        notifyDataSetChanged()
    }
}
~~~
* 使用Glide加载图片


#### 第四步：UI展示
[回到上一级](#周视图番组表)

在 presentation/ui/fragment 下创建 AnimeSchcheduleFragment.kt 用于调用 BangumiApi **获取数据**，并调用 AnimeAdapter 将列表中的每一条**数据绑定**在UI上，同时**监听点击事件**，处理点击事件，将日程**持久化**到数据库中

~~~kotlin
class AnimeScheduleFragment: Fragment() {

    companion object{
        private const val ARG_DATE = "ARG_DATE"

        fun newInstance(date: LocalDate): AnimeScheduleFragment{
            val fragment = AnimeScheduleFragment()
            val args = Bundle()
            args.putSerializable(ARG_DATE, date.toString())
            fragment.arguments = args
            return fragment
        }
    }

    private lateinit var recyclerView: RecyclerView
    private lateinit var animeAdapter: AnimeAdapter
    private lateinit var bangumiApiService: BangumiApiService
    private lateinit var eventColorManager: EventColorManager
    lateinit var sharedViewModel: SharedViewModel
    private var currentDate: LocalDate? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        sharedViewModel = ViewModelProvider(requireActivity())[SharedViewModel::class.java]

        arguments?.getString(ARG_DATE)?.let {
            currentDate = LocalDate.parse(it)
        } ?: run {
            currentDate = LocalDate.now()
        }
    }

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        val view = inflater.inflate(R.layout.fragment_anime_schedule, container, false)

        recyclerView = view.findViewById(R.id.animeRecyclerView)
        recyclerView.layoutManager = LinearLayoutManager(requireContext())

        animeAdapter = AnimeAdapter(emptyList()){anime ->
            addToCalendar(anime)
        }
        recyclerView.adapter = animeAdapter

        bangumiApiService = BangumiApiService(requireContext())

        currentDate?.let { loadAnimeForDate(it) } ?: loadAnimeForDate(LocalDate.now())
        return view
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        // 监听日期变化
        sharedViewModel.selectedDate.observe(viewLifecycleOwner){ date ->
            loadAnimeForDate(date)
        }

    }

    private fun loadAnimeForDate(date: LocalDate) {
        currentDate = date
        bangumiApiService.getBangumiForDate(date){ animeList ->
            activity?.runOnUiThread {
                if (animeList != null){
                    animeAdapter.updateAnimeList(animeList)
                }else{
                    Toast.makeText(requireContext(), "获取动漫列表失败", Toast.LENGTH_SHORT).show()
                }
            }
        }
    }

    private fun addToCalendar(anime: BangumiItem){
        // 获取选中的日期
        val selectedDate = sharedViewModel.selectedDate.value ?: LocalDate.now()

        // 解析播出时间（使用选中的日期，而不是动漫的原始播出日期）
        val airDateTime = LocalDateTime.of(selectedDate, LocalTime.of(9, 0))

        // 设置提醒时间为播出前15分钟
        val reminderTime = airDateTime.minusMinutes(15)

        val title = anime.name_cn.ifEmpty { anime.name }
        val content = buildString {
            append("番剧名称: ${anime.name_cn.ifEmpty { anime.name }}\n")
            append("原名: ${anime.name}\n")
            append("播出日期: ${anime.air_date}\n")
            append("Bangumi链接: ${anime.url}")
        }

        addEvent(
            title = title,
            description = content,
            date = selectedDate,  // 使用选中的日期
            reminderTime = reminderTime
        )
    }

    private fun addEvent(
        title: String,
        description: String,
        date: LocalDate,
        reminderTime: LocalDateTime
    ) {
        CoroutineScope(Dispatchers.IO).launch {
            try {
                val database = CalendarDatabase.getInstance(requireContext())
                val eventDao = database.eventDao()
                eventColorManager = EventColorManager()

                // 检查是否存在同名事件
                val startOfDay = date.atStartOfDay(ZoneId.systemDefault()).toInstant().toEpochMilli()
                val endOfDay = date.atTime(23, 59, 59).atZone(ZoneId.systemDefault()).toInstant().toEpochMilli()

                val existingEventCount = eventDao.getEventCountByTitleAndDate(title, startOfDay, endOfDay)

                if (existingEventCount > 0) {
                    // 如果已经存在同名事件，显示提示信息
                    withContext(Dispatchers.Main) {
                        Toast.makeText(requireContext(), "该动画已在当天日程中", Toast.LENGTH_SHORT).show()
                    }
                    return@launch
                }

                val existsColors = getExistingEventColorsForDate(date)

                // 创建事件
                val event = CalendarEvent(
                    id = 0,
                    title = title,
                    content = description,
                    startTime = date.atStartOfDay(ZoneId.systemDefault()).toInstant().toEpochMilli(),
                    endTime = date.atTime(23, 59, 59).atZone(ZoneId.systemDefault()).toInstant().toEpochMilli(),
                    eventColor = eventColorManager.getColorForEvent(existsColors),
                    reminderTime = reminderTime.atZone(ZoneId.systemDefault()).toInstant().toEpochMilli(),
                    isCompleted = false
                )

                eventDao.insertEvent(event)

                // 切换到主线程显示 Toast
                withContext(Dispatchers.Main) {
                    Toast.makeText(requireContext(), "${event.title}已添加到日程", Toast.LENGTH_SHORT).show()
                    EventUpdateManager.getInstance().notifyEventAdded()
                    sharedViewModel.notifyNewEventAdded(date)
                }
            } catch (e: Exception){
                e.printStackTrace()
            }
        }
    }

    private suspend fun CoroutineScope.getExistingEventColorsForDate(selectedDate: LocalDate?): List<Int> {
        // 1. 检查日期是否为空
        if (selectedDate == null){
            return emptyList()
        }

        // 2. 计算时间范围
        val startOfDay = selectedDate.atStartOfDay(ZoneId.systemDefault()).toInstant().toEpochMilli()
        val endOfDay = selectedDate.atTime(23, 59, 59).atZone(ZoneId.systemDefault()).toInstant().toEpochMilli()

        // 3. 查询数据库获取指定日期的所有事件
        val database = CalendarDatabase.getInstance(requireContext())
        val events = async {
            database.eventDao().getEventsInRange(startOfDay, endOfDay)
        }.await()

        // 4. 提取事件颜色
        return events.map { it.eventColor }
    }
}
~~~

#### 第五步：优化缓存
[回到上一级](#周视图番组表)

使用缓存可以减少网络请求，提高性能。

在 domain/utils 下创建 CacheManager.kt 文件，用于管理缓存

~~~kotlin
class CacheManager private constructor(context: Context){
    companion object{
        private const val CACHE_SIZE = (200 * 1024 * 1024).toLong() // 200 MB
        private const val CACHE_TIME = 60 * 60 * 24 * 7 // 7天

        @Volatile
        private var INSTANCE: CacheManager? = null

        fun getInstance(context: Context): CacheManager{
            return INSTANCE?: synchronized(this){
                INSTANCE ?: CacheManager(context.applicationContext).also { INSTANCE = it }
            }
        }
    }

    private val cache: Cache = Cache(File(context.cacheDir, "http_cache"), CACHE_SIZE)

    private val cacheInterceptor = Interceptor { chain ->
        val request = chain.request()
        Log.d("CacheManager", "发起请求：${request.url}")

        val originalResponse = chain.proceed(request)

        // 检查是否来自缓存
        val isFromCache =
            originalResponse.header("X-Android-Response-Source")?.contains("CACHE") ?: false
        if (isFromCache) {
            Log.d("CacheManager", "来自缓存：${request.url}")
        } else {
            Log.d("CacheManager", "来自网络：${request.url}")
        }
        // 删除不存在的引用，直接构建缓存控制头部
        originalResponse.newBuilder()
            .header("Cache-Control", "public, max-age=$CACHE_TIME")
            .removeHeader("Pragma")
            .build()
    }

    private val offlineCacheInterceptor = Interceptor { chain ->
        var request = chain.request()

        if (!isNetworkAvailable(chain)) {
            request = request.newBuilder()
                .header("Cache-Control", "public, only-if-cached, max-stale=" + CACHE_TIME)
                .build()
        }

        chain.proceed(request)
    }

    val okHttpClient: OkHttpClient = OkHttpClient.Builder()
        .cache(cache)
        .addNetworkInterceptor(cacheInterceptor)
        .addInterceptor(offlineCacheInterceptor)
        .connectTimeout(30, TimeUnit.SECONDS)
        .readTimeout(30, TimeUnit.SECONDS)
        .writeTimeout(30, TimeUnit.SECONDS)
        .build()

    private fun isNetworkAvailable(chain: Interceptor.Chain): Boolean{
        val connectivityManager = chain.connection()?.route()?.address?.toString()
        // 检查网络状态的实际实现
        return true
    }
}
~~~
* 使用单例实例，并且使用注解@Volatile修饰，确保多个线程可见
* cacheInterceptor: 缓存拦截器，用于缓存请求和响应数据
* offlineInterceptor: 离线拦截器，用于处理无网络情况
* okHttpClient: 创建OkHttpClient实例，并设置超时时间为30秒








