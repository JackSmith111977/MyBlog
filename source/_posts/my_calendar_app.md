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
   * 首先通过 binding.viewPager 获取应用中的ViewPager组件
   * 通过 viewPager.currentItem 获取当前显示页面的索引
   * 使用 supportFragmentManager.findFragmentByTag("f" + viewPager.currentItem) 根据标签查找当前Fragment
     * ViewPager2会自动为每个页面的Fragment分配标签，格式为"f"+页面索引
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





























