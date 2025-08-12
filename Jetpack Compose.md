# Jetpack Compose

🔗 [「Android 基本概念：使用 Compose」課程  |  Android Developers](https://developer.android.com/courses/android-basics-compose/course?hl=zh-tw)

🔗 [Jetpack Compose  |  Android Developers](https://developer.android.com/courses/pathways/compose?hl=zh-tw)

---

## 什麼是 Jetpack Compose？
Jetpack Compose 是一種**新型工具包**，用於**建構原生 Android UI**

主要目的是**簡化並加快 Android 平台的 UI 開發作業**，其主要優勢包括：
* 程式碼更少
* 符合直覺
* 提升開發效率
* 功能強大

## Jetpack Compose 與傳統 XML 佈局核心差異
| 項目 | 現代 Jetpack Compose | 傳統 XML（View System） |
| -------- | -------- | -------- |
| 開發模式     | 宣告式 UI，Kotlin 撰寫 UI 與邏輯     | 命令式 UI，XML 定義介面、程式控制邏輯     |
| 靈活性     | 高度組合、可重用，狀態變化自動更新     | 手動更新 View，結構較固定     |
| 維護成本     | 單一語言，減少樣板碼，易測試     | XML 與程式碼分離，維護成本高     |
| 工具支援     | 即時預覽（Live Preview）、互動編輯     | 傳統 Layout Editor，預覽較慢     |

## 傳統 XML 範例
```xml=
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center">

    <TextView
        android:id="@+id/textView"
        android:text="Hello XML!"
        android:textSize="20sp"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

    <Button
        android:id="@+id/button"
        android:text="Click Me"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

</LinearLayout>
```
```kotlin=
class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val textView = findViewById<TextView>(R.id.textView)
        val button = findViewById<Button>(R.id.button)

        button.setOnClickListener {
            textView.text = "Button Clicked!"
        }
    }
}
```
<img width="1666" height="907" alt="image" src="https://github.com/user-attachments/assets/1dc97582-8ed4-402b-a742-146d82d04849" />

## Jetpack Compose 範例
```kotlin=
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            GreetingScreen()
        }
    }
}

@Composable
fun GreetingScreen() {
    var text by remember { mutableStateOf("Hello Compose!") }

    Column(
        modifier = Modifier.fillMaxSize(),
        verticalArrangement = Arrangement.Center,
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        Text(text, fontSize = 20.sp)
        Button(onClick = { text = "Button Clicked!" }) {
            Text("Click Me")
        }
    }
}

@Preview(showBackground = true, showSystemUi = true)
@Composable
fun GreetingScreenPreview() {
    GreetingScreen()
}
```
<img width="1775" height="862" alt="image" src="https://github.com/user-attachments/assets/8138cd84-5055-4678-9d37-7d97c8d2e879" />
