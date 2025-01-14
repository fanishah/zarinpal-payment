<div dir="rtl">
<p align="center">
<img src="https://s2.uupload.ir/files/4534_aeg.png" alt="Logo" height=170>
<h1 align="center">ZarinPal Pay</h1>
با استفاده از این پکیچ می توانید به صورت آسان از درگاه بانکی زرین پال در پروژه های Node.js خود استفاده کنید.
</p>
</div>
<div align="center">
  <br />
  <a href="https://fanishah.ir">fanishah.ir</a>
  <span>&nbsp;&nbsp;•&nbsp;&nbsp;</span>
  <a href="https://github.com/fanishah/zarinpal-pay/issues">Issues</a>
  <span>&nbsp;&nbsp;•&nbsp;&nbsp;</span>
  <a href="https://npmjs.com/package/eazy-idpay">Eazy-IdPay</a>
  <span>&nbsp;&nbsp;•&nbsp;&nbsp;</span>
  <a href="https://link.fanishah.ir/zarinpal-pay-exemple">نمونه کد</a>
  <br />
</div>

<div dir="rtl">

# روش نصب

</div>

```
npm i zarinpal-pay
```

<div dir="rtl">

# متد ها

  </div>
  
```
create() // ایجاد تراکنش
verify() // اعتبارسنجی تراکنش
unverified() // ليست پرداخت هاي موفق اخیر
```

  <div dir="rtl">

# احراز هویت درگاه

  </div>
  
```
import  ZarinpalPayment from  "zarinpal-pay";
const zarinpal = new ZarinpalPayment (Merchant , Option);
```
  <div dir="rtl">
  
**پارمتر Merchant**

- كد 36 كاراكتری اختصاصی برای احراز هویت درگاه

**پارامتر Option (اختیاری)**

- این پارامتر شامل یک آبجکت با دو ورودی isSandbox و isToman اختیاری می باشد
  > ورودی isToman : واحد پولی درگاه که صورت پیشفرض ریال می باشد. مقدار به صورت پیشفرض false می باشد و در صورت true واحد پولی درگاه تومان میشود.
  >
  > ورودی isSandbox : برای فعال سازی درگاه آزمایشی به صورت پیشفرض false می باشد در صورت true بودن درگاه به صورت آزمایشی می باشد. برای استفاده از درگاه آزمایشی باید برای قسمت Merchant کد 36 کارکتر دلخواه مانند eaa46b01-819e-42ef-8a67-ba2bb7f69a32  وارد نمایید
  </div>
```
new ZarinpalPayment(Merchant , { isToman : true, isSandbox : true })
```
  <div dir="rtl">
  
**درگاه آزمایشی**

- برای فعال سازی درگاه آزمایشی باید مقدار  isSandbox که در آبجکت Option هست را true وارد کنید 

  </div>
```
new ZarinpalPayment("eaa46b01-819e-42ef-8a67-ba2bb7f69a32" , { isSandbox : true })
```
<div dir="rtl">

  # ایجاد تراکنش

  <p>
  برای ایجاد تراکنش از متد create استفاده کنید

  </p>

  </div>

```
const createTransaction = await zarinpal.create({
	amount: 100000,
	callback_url: "http://localhost:3000/callback",
	mobile: "09339993377",
	email: "my@site.com",
	description: "توضیحات تراکنش",
	order_id: "3010",
});
```

  <div dir="rtl">
	
  در صورت درست بودن تمام ورودی های متد پاسخ زیر برای شما به صورت جیسون به شما داده میشود و مقدار code باید 100 باشد
</div>

```
{
    "data":{
        "code": 100,
        "message": "Success",
        "authority": "A00000000000000000000000000217885159",
        "fee_type": "Merchant",
        "fee": 100,
        link:"https://payment.zarinpal.com/pg/StartPay/A00000000000000000000000000217885159"
	},
	"errors": []
}
```

<div dir="rtl">
  
   شما باید برای انجام مراحل پرداخت کاربر را به  پراپرتی link در پاسخ ایجاد تراکنش ریدایرکت کنید

در صورت بروز خطا در ایجاد تراکنش پاسخی مشابه زیر به صورت جیسون دریافت می کنید

</div>

```
{
  code: -9,
  message: 'The input params invalid, validation error.',
  validations: [ { amount: 'The amount must be at least 1000.' } ]
}
```

[ لیست خطاها](https://www.zarinpal.com/docs/paymentGateway/errorList.html)

| اجباری | نوع مقدار | توضیحات                            | ورودی        |
| ------ | --------- | ---------------------------------- | ------------ |
| بله    | عدد       | مبلغ تراكنش                        | amount       |
| بله    | رشته      | صفحه بازگشت پس از انجام عمل پرداخت | callback_url |
| خیر    | رشته      | شماره تماس خریدار                  | mobile       |
| خیر    | رشته      | ایمیل خریدار                       | email        |
| خیر    | رشته      | توضیحات مربوط به تراکنش            | description  |
| خیر    | رشته      | شماره سفارش                        | order_id     |

<div dir="rtl">

# تایید تراکنش

این مرحله زمانی انجام میشه که کاربر از درگاه پرداخت بر میگرده. در این مرحله با توجه به نتیجه تراکنش و وضعیت اون ، زرین پال کاربر را به آدرس کال بک که در زمان ایجاد تراکنش در ورودی callback_url است ریدایرکت می کند. در آدرس ریدایرکت شده دو پارامتر Authority و Status وجود دارد. که Authority شناسه یکتا تراکنش و Status وضعیت پرداخت تراکنش است.

</div>

<div dir="rtl">
   
نكته: توجه داشته باشيد كه يك Status به صورت QueryString به سايت آدرس کال بک ارسال ميگردد كه دو مقدار ثابت دارد ”OK“ و”NOK“ ؛ در صورتي كه اين مقدار برابر ”NOK“ بود به اين معنا بوده كه تراكنش نا موفق بوده و يا توسط كاربر لغو شده است؛ پس در صورتي verify استفاده شود كه با QueryString مقدار Status برابر با ”OK“ باشد.

</div>
<p align="center" style="color:green">مثال تراکنش موفق</p>

`http://www.yoursite.ir/Authority=A00000000000000000202690354&Status=OK`

<p align="center" style="color:red">
مثال تراکنش ناموفق
</p>

`http://www.yoursite.ir/Authority=A00000000000000000202690354&Status=NOK`

<div dir="rtl">
بعد از بررسی Status ادرس کال بک شما می توانید با استفاده از متد Verify تراکنش را اعتبارسنجی کنید که آیا کاربر تراکنش را با موفقیت پرداخت کرده است یا نه.

  </div>

```
const verifyTransaction = await zarinpal.verify({authority , amount});

```

| اجباری | نوع  | ورودی     |
| ------ | ---- | --------- |
| بله    | عدد  | amount    |
| بله    | رشته | authority |


```
const verifyTransaction = await zarinpal.verify({authority: "A00000000000000000202690354" , amount: 10000});

```

<div dir="rtl">
در پاسخ درخواست اعتبارسنجی تراکنش با متد Verify یک پاسخ به شما داده می شد کهه اگر مقدار پارامتر code برابر 100 بود به معنای موفق بودن تراکنش است و با پارامتر ref_id شماره تراکنش را به کاربر نمایش میدهید. در صورتی که کاربر تراکنش را پرداخت کرده باشد پاسخ متد verify مشابه زیر است توجه کنید باید مقدار code عدد 100 باشد.
</div>

```
{
    "data": {
        "code": 100,
        "message": "Verified",
        "card_hash": "1EBE3EBEBE35C7EC0F8D6EE4F2F859107A87822CA179BC9528767EA7B5489B69",
        "card_pan": "502229******5995",
        "ref_id": 201,
        "fee_type": "Merchant",
        "fee": 0
    },
    "errors": []
}
```

<div dir="rtl">
نکته خیلی مهم : درصورت موفقیت آمیز بودن تراکنش، با فراخوانی متد verify، یکبار کد مقدار code  برابر با 100 می باشد و در دفعات بعدی وریفای همان تراکنش مقدار code  برابر با 101 می باشد.
 </div>
<div dir="rtl">

کد code 101 به معنای آن است که تراکنش موفق بوده و یکبار قبلا وریفای شده است و این بار دوم هست

 </div>

 <div dir="rtl">

# متد unverified

ممكن است شما نياز داشته باشيد كه متوجه شويد چه پرداخت هاي توسط وب سرويس شما به درستي انجام شده اما متد verify روي آنها اعمال نشده ، به عبارت ديگر اين متد ليست پرداخت هاي موفقي كه شما آنها را تصديق نكرده ايد را به شما نمايش مي دهد.
</div>

```
await zarinpal.verify({authority , amount})
```

<p align="right">
نمونه اطلاعات بازگشتی :
</p>

```
{
    "data": {
        "code": "100",
        "message": "Success",
        "authorities": [
            {
                "authority": "A00000000000000000000000000207288780",
                "amount": 50500,
                "callback_url": "https://golroz.com/vpay",
                "referer": "https://golroz.com/test-form/",
                "date": "2020-07-01 17:33:25"
            },
            {
                "authority": "A00000000000000000000000000206873220",
                "amount": 11100,
                "callback_url": "zarin_link",
                "referer": "/",
                "date": "2020-06-27 10:22:02"
            }
        ]
    }
}
```