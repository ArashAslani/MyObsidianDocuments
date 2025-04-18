زمانی استفاده میشوند که میخوام یک متد را به کلاسی که دسترسی نداریم اضافه کنیم. مانند کلاس string
این متد ها در کلاس static و روی یک متد static نوشته می شوند.
public static string Right(this string s, int count)

که به صورت زیر صدا زده میشوند.
var salam = "salam"
salam.Right(5);