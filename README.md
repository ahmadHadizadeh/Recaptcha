How Create Recaptcha Google for Login website

نحوه ساخت google Recaptcha در قسمت login website
============
##### 1-وارد اکانت خود در گوگل می شویم


##### 2-وارد قسمت گوگل ریکپچا میشویم: https://google.com/recaptcha/admin/create 
##### 3-   SiteKey , SecretKey   تکمیل فرایند ساخت ریکپچا در سایت گوگل  با پر کردن فیلد های  مربوطه برای گرفتن


#####  4-ابتدا کتابخانه NewtonSoft.json را در Nuget به پروژه خود اضافه میکینیم


##### 5-اضافه کردن اسکریپت های گوگل ریکپچا به پروژه

 - <scriptsrc="https://www.google.com/recaptcha/api.js" async defer></script> 

##### 6- اضافه کردن  تصویر ریکپجا به ویو لاگین پروژه و دادن   sitkey در ویو

                                <div class="row">
                                        <div class="col-lg-12  col-md-10">
                                            <div class="mb-3">
                                                <div class="form-group">
                                                    <div style="direction: ltr;" class="g-recaptcha"  data-sitekey="DZURUQDZUQ6YrvPhURAAA9r-_DNKU2a6bQ"></div>
                                                  
                                                </div>
                                            </div>
                                        </div>
                                    </div>


##### 7- اضافه کردن یک کلاس جدید به پروژه
        public class ReCaptchaResponse
        {

        [JsonProperty("success")]
        public bool Success { get; set; }

        [JsonProperty("challenge_ts")]
        public string ValidatedDateTime { get; set; }

        [JsonProperty("hostname")]
        public string HostName { get; set; }

        [JsonProperty("error-codes")]
        public List<string> ErrorCodes { get; set; }
        }


##### کدهای کنترلر لاگین - LoginCode

        [HttpPost]
        [Route("Login")]
 
        public ActionResult Login(LoginViewModel login, IFormCollection form)
        {
            #region CaptchCode
            string urlToPost = "https://www.google.com/recaptcha/api/siteverify";
            string secretKey = "6LeMLYUdAAAAAM0lP2-NZ7y2GJaUFleZRMnYoEEd"; // change this
            string gRecaptchaResponse = form["g-recaptcha-response"];

            var postData = "secret=" + secretKey + "&response=" + gRecaptchaResponse;

            // send post data
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(urlToPost);
            request.Method = "POST";
            request.ContentLength = postData.Length;
            request.ContentType = "application/x-www-form-urlencoded";

            using (var streamWriter = new StreamWriter(request.GetRequestStream()))
            {
                streamWriter.Write(postData);
            }

            // receive the response now
            string result = string.Empty;
            using (HttpWebResponse response = (HttpWebResponse)request.GetResponse())
            {
                using (var reader = new StreamReader(response.GetResponseStream()))
                {
                    result = reader.ReadToEnd();
                }
            }

            // validate the response from Google reCaptcha

            var captChaesponse = JsonConvert.DeserializeObject<ReCaptcha>(result);

            if (!captChaesponse.Success)
            {
                ViewBag.Message = "Sorry, please validate the reCAPTCHA";
                return View();
            }
            // go ahead and write code to validate username password against database
            #endregion

            if (!ModelState.IsValid)
            {
                return View(login);
            }

            var user = _userService.LoginUser(login);
            if (user != null)
            {
                if (user.IsActive)
                {
                    var claims = new List<Claim>()
                    {
                        new Claim(ClaimTypes.NameIdentifier,user.UserId.ToString()),
                        new Claim(ClaimTypes.Name,user.UserName)
                    };
                    var identity = new ClaimsIdentity(claims, CookieAuthenticationDefaults.AuthenticationScheme);
                    var principal = new ClaimsPrincipal(identity);

                    var properties = new AuthenticationProperties
                    {
                        IsPersistent = login.RememberMe
                    };
                    HttpContext.SignInAsync(principal, properties);

                   
            return View(login);
        }

##### [ نحوه ساخت google Recaptcha در قسمت login website](https://www.asancode.com/c97e2)
##### 

#### [How Create Recaptcha Google for Login website](https://www.asancode.com/c/97e2)

##### [Sourc:document 📃 Google recaptcha](developers.google.com/recaptcha/docs/display)


## [آموزش Google reCaptcha در Asp.net Core](https://www.asancode.com/c/97e2)
