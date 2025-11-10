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



















