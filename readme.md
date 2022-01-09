<table style="width: 100%;">
  <tr>
    <td style="text-align: center; border: none;">
    Министерство образования и науки РФ<br>
Государственное бюджетное профессиональное образовательное учреждение Республики Марий Эл<br>
Йошкар-Олинский технологический колледж
</td>
  </tr>
  <tr>
    <td style="text-align: center; border: none; height: 15em;">
    <h2 style="font-size:3em;">Реферат</h2>
      <h3>по дисциплине "Проектирование и дизайн Информационных систем"<br><br> Тема:<b> "Рисование графиков C# с использованием штатных библиотек"<b> </h3></td>
  </tr>
  <tr>
    <br><br><td style="text-align: right; border: none; height: 20em;">
      Разработала:<br/>
      Кропотова Юлия <br>
      Группа: И-31<br>
      Преподаватель:<br>
      Колесников Евгений Иванович
    </td>
  </tr>
  <tr>
    <td style="text-align: center; border: none; height: 5em;">
    г.Йошкар-Ола,<br> 2021</td>
  </tr>
</table>
<div style="page-break-after: always;"></div>

# Описание

Существует множество библиотек для построения графиков. Выбор в сторону решения от Microsoft пал на то, что оно встроено в .NET Framework 4 и потому не требует подключения сторонних библиотек. Хотя есть и один недостаток — работать с ним можно только на форме Windows Forms, для WPF форм штатной работы с компонентом не предусмотрено. Из-за этого все мануалы, начинающиеся словами «перетащите компонент Chart на форму» абсолютно не помогали решить поставленную проблему.

# Начало работы

Для этого (судя по инструкции с MSDN) необходимо добавить ссылку на WindowsFormsIntegration и на System.Windows.Forms. Также необходимо добавить их пространства имен в элемент <Window> XAML-документа формы, и элемент <WindowsFormsHost>, в котором впоследствии будет размещаться нужный компонент Windows Forms:

```
<Window xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
xmlns:wfi="clr-namespace:System.Windows.Forms.Integration;assembly=WindowsFormsIntegration"
xmlns:wf="clr-namespace:System.Windows.Forms;assembly=System.Windows.Forms">
<StackPanel>
<WindowsFormsHost></WindowsFormsHost>
</StackPanel>
</Window>
```

## (что написано в инструкции MSDN)

Добавьте ссылки на следующие сборки:

**WindowsFormsIntegration**

**System.Windows.Forms**

Откройте MainWindow.xaml в конструкторе WPF. В элементе Window добавьте следующее сопоставление пространства имен. `wf`
oтображение пространства имен устанавливает ссылку на сборку, которая содержит элемент управления Forms Windows.

XAML:

```
xmlns:wf="clr-namespace:System.Windows.Forms;assembly=System.Windows.Forms"
```

В элементе Grid добавьте следующий код XAML.

MaskedTextBox управления создается как дочерний WindowsFormsHost управления.

тоже XAML:

```
<Grid>

    <WindowsFormsHost>
        <wf:MaskedTextBox x:Name="mtbDate" Mask="00/00/0000"/>
    </WindowsFormsHost>

</Grid>
```

Теперь все готово к использованию компонентов Windows Forms, но всю работу по внедрению компонента на форму придется делать вручную. Подключим к проекту System.Windows.Forms.DataVisualization.Charting, добавим пространство имен в XAML-документ и сам компонент на форму.

```
<Window xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
...
xmlns:dvc="clr-namespace:System.Windows.Forms.DataVisualization.Charting; assembly=System.Windows.Forms.DataVisualization">
<StackPanel>
<WindowsFormsHost><dvc:Chart x:Name="chart" /></WindowsFormsHost>
</StackPanel>
</Window>
```

Есть замечательный проект от Microsoft, содержащий массу примеров работы с компонентом Chart для Windows Forms. Но запустить его получится не сразу, так как нужный для инициализации компонента код под Windows Forms среда генерирует в методе InitializeComponent() сама на основе сделанных разработчиком настроек компонента. Я не нашла способа вызова конфигуратора компонента для WPF формы, поэтому прежде чем использовать код из примеров, надо будет вручную дописать пару строк кода.

```
using System.Windows.Forms.DataVisualization.Charting;
...

private void Window_Loaded(object sender, RoutedEventArgs e)
{
// Все графики находятся в пределах области построения ChartArea, создадим ее
chart.ChartAreas.Add(new ChartArea("Default"));

// Добавим линию, и назначим ее в ранее созданную область "Default"
chart.Series.Add(new Series("Series1"));
chart.Series["Series1"].ChartArea = "Default";
chart.Series["Series1"].ChartType = SeriesChartType.Line;

// добавим данные линии
string[] axisXData = new string[] {"a", "b", "c"};
double[] axisYData = new double[] {0.1, 1.5, 1.9};
chart.Series["Series1"].Points.DataBindXY(axisXData, axisYData);
}
```

В результате получим (5 за реферат) замечательный график, и главное никаких сторонних библиотек.
