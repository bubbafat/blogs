---
author: admin
comments: false
date: 2014-03-20 19:45:28+00:00
layout: post
link: https://roberthorvick.com/2014/03/20/twilio-web-api-nuget-package-released/
slug: twilio-web-api-nuget-package-released
title: Twilio Web API nuget package released
wordpress_id: 371
---

Every time I start creating a Twilio Web API application I always get a little frustrated.  It's not that using their API is hard - it certainly isn't.  Rather, their API does not play well with ASP.NET Web API.  

Basically there are two problems:





  * [TwilioController](https://github.com/twilio/twilio-csharp/blob/master/src/Twilio.Mvc/TwilioController.cs) does not inherit form ApiController


  * [ValidateRequestAttribute](https://github.com/twilio/twilio-csharp/blob/master/src/Twilio.Mvc/ValidateRequestAttribute.cs) is not compatible with Web API



Neither of these are huge problems but they make using Web API more of a hassle than MVC - and then I end up either using MVC when I don't want to or I copy and paste code from previous workarounds.

But now I've had enough.  So I set aside a couple hours and put together a proper (for me) Twilio Web Api assembly and nuget package.

Relevant links:

NuGet Package: [https://www.nuget.org/packages/Twilio.WebApi/](https://www.nuget.org/packages/Twilio.WebApi/)

Source: [https://github.com/bubbafat/Twilio-WebAPI/](https://github.com/bubbafat/Twilio-WebAPI/)

Twilio's approach:  [https://www.twilio.com/blog/2012/11/building-twilio-apps-using-asp-net-mvc-4-web-api.html](https://www.twilio.com/blog/2012/11/building-twilio-apps-using-asp-net-mvc-4-web-api.html)



## Twilio Web API sample code



Here's a Twilio Web API controller (deriving from our Web API TwilioController class) that uses the Web API ValidateRequest attribute to secure the API and performs a simple echo (using the TwilioController's TwiML method).


    
    
    public class SmsController : TwilioController
    {
        [HttpPost]
        [ValidateRequest("YOUR AUTH KEY")]
        public IHttpActionResult Echo(SmsRequest request)
        {
            TwilioResponse response = new TwilioResponse();
            response.Sms(request.Body);
            return TwiML(response);
        }
    }
    



Exactly what the MVC version looks like.
