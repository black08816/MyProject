---
title: Jetpack Compose 導覽架構與底部導覽範例

---

# Jetpack Compose 導覽架構與底部導覽範例

![Screenshot_20250909_105305](https://hackmd.io/_uploads/Sk2VcG6cxg.png)

---

## 1. 各個頁面畫面 (Screen Composables)
這些是單純的 UI 畫面，利用 `Box` 置中顯示標題文字。

```kotlin!=
@Composable
fun HomeScreen() {
    Box(
        modifier = Modifier.fillMaxSize(),
        contentAlignment = Alignment.Center
    ) {
        Text(
            text = "Home Screen",
            style = MaterialTheme.typography.headlineLarge
        )
    }
}

@Composable
fun ProfileScreen() {
    Box(
        modifier = Modifier.fillMaxSize(),
        contentAlignment = Alignment.Center
    ) {
        Text(
            text = "Profile Screen",
            style = MaterialTheme.typography.headlineLarge
        )
    }
}

@Composable
fun CartScreen() {
    Box(
        modifier = Modifier.fillMaxSize(),
        contentAlignment = Alignment.Center
    ) {
        Text(
            text = "Cart Screen",
            style = MaterialTheme.typography.headlineLarge
        )
    }
}

@Composable
fun SettingsScreen() {
    Box(
        modifier = Modifier.fillMaxSize(),
        contentAlignment = Alignment.Center
    ) {
        Text(
            text = "Settings Screen",
            style = MaterialTheme.typography.headlineLarge
        )
    }
}
```

👉 每個 `Screen` 負責顯示一個頁面，未來可以在這裡放入對應的功能內容，例如首頁清單、購物車清單、設定選單等。

---

## 2. 導覽項目定義 (NavigationItem)
利用 `sealed class` 封裝所有底部導覽的項目，包含 **路徑(route)、標題字串、圖示(選取與未選取)**。

```kotlin!=
sealed class NavigationItem(
    val route: String,
    @StringRes val titleTextId: Int,
    val selectedIcon: ImageVector,
    val unselectedIcon: ImageVector
) {
    object Home : NavigationItem(
        route = "home",
        titleTextId = R.string.home,
        selectedIcon = Icons.Filled.Home,
        unselectedIcon = Icons.Outlined.Home
    )
    object Profile : NavigationItem(
        route = "profile",
        titleTextId = R.string.profile,
        selectedIcon = Icons.Filled.Person,
        unselectedIcon = Icons.Outlined.Person
    )
    object Cart : NavigationItem(
        route = "cart",
        titleTextId = R.string.cart,
        selectedIcon = Icons.Filled.ShoppingCart,
        unselectedIcon = Icons.Outlined.ShoppingCart
    )
    object Settings : NavigationItem(
        route = "settings",
        titleTextId = R.string.settings,
        selectedIcon = Icons.Filled.Settings,
        unselectedIcon = Icons.Outlined.Settings
    )
}

val navigationItems = listOf(
    NavigationItem.Home,
    NavigationItem.Profile,
    NavigationItem.Cart,
    NavigationItem.Settings
)
```

👉 好處：
* 中央化管理導航資訊
* 減少硬編碼 (避免手動輸入 route 或 titleId 錯誤)
* 更容易擴充（新增一個頁面只要新增一個 `object`）

---

## 3. AppNavHost (導航容器)
這裡用 `NavHost` 定義 **路由對應的 Composable 頁面**。

```kotlin!=
@Composable
fun AppNavHost(navController: NavHostController, modifier: Modifier = Modifier) {
    NavHost(
        navController = navController,
        startDestination = NavigationItem.Home.route,
        modifier = modifier
    ) {
        composable(route = NavigationItem.Home.route) { HomeScreen() }
        composable(route = NavigationItem.Profile.route) { ProfileScreen() }
        composable(route = NavigationItem.Cart.route) { CartScreen() }
        composable(route = NavigationItem.Settings.route) { SettingsScreen() }
    }
}
```

👉 功能：
* 負責畫面之間的切換
* 透過 `startDestination` 指定進入 App 的第一個畫面

---

## 4. BottomNavigationBar (底部導覽列)
定義底部導覽 UI，使用 `NavigationBar` 與 `NavigationBarItem`。

```kotlin!=
@Composable
fun BottomNavigationBar(navController: NavHostController) {
    val navBackStackEntry by navController.currentBackStackEntryAsState()
    val currentDestination = navBackStackEntry?.destination

    NavigationBar(containerColor = Color.White) {
        navigationItems.forEach { item ->
            val selected = currentDestination?.hierarchy?.any { it.route == item.route } == true
            NavigationBarItem(
                icon = { Icon(imageVector = if (selected) item.selectedIcon else item.unselectedIcon, contentDescription = stringResource(item.titleTextId)) },
                label = { Text(text = stringResource(item.titleTextId)) },
                selected = selected,
                onClick = {
                    navController.navigate(route = item.route) {
                        popUpTo(navController.graph.findStartDestination().id) {
                            saveState = true
                        }
                        launchSingleTop = true
                        restoreState = true
                    }
                }
            )
        }
    }
}
```

👉 功能重點：
* 根據 `navController.currentBackStackEntryAsState()` 動態判斷目前頁面
* 點擊 item 會導航至指定頁面
* `popUpTo` + `saveState` + `restoreState` → 保持頁面狀態（避免重複建立頁面）

---

## 5. MainActivity 與 Scaffold 架構
進入 App 的入口，結合 **Scaffold + NavHost + BottomNavigation**。

```kotlin!=
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContent {
            MPOSDemoTheme {
                MainScreen()
            }
        }
    }
}

@Composable
fun MainScreen() {
    val navController = rememberNavController()

    Scaffold(
        bottomBar = { BottomNavigationBar(navController = navController) },
        modifier = Modifier.fillMaxSize()
    ) { innerPadding ->
        AppNavHost(
            navController = navController,
            modifier = Modifier.padding(innerPadding)
        )
    }
}
```

👉 功能重點：
* `Scaffold` 提供結構化 UI（支援 TopBar / BottomBar / FAB 等）
* `BottomNavigationBar` 固定顯示在底部
* `AppNavHost` 顯示對應頁面內容

---

📌 總結：
這份架構已經完成 **底部導覽 + Navigation** 的標準實作。
未來如果要 **新增一個頁面**，只要：
1. 在 `NavigationItem` 加一個 `object`
2. 在 `AppNavHost` 增加一個 `composable` 就能快速擴充。
