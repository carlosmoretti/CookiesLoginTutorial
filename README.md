## Por que usar autenticação por Cookies e não por Session?
Usando autenticação por sessions, temos problemas ao trabalharmos com load-balancers, visto que multiplos servidores estão rodando diversas instâncias do mesmo sistema. Logo, sua session estará em servidor 1, mas não em servidor 2, podendo em determinado ponto do sistema você acabar ficando sem sessão por ter entrado na zona de outro servidor.

## Como implementar?
No startup.cs, em Services, adicionar o seguinte código:
```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme).AddCookie();  
```
Em Configure, adicionar o código abaixo:
```csharp
app.UseAuthentication();  
```
No seu controller, faça o seu tratamento e adicione "Claims" para quais dados você deseja armazenar do usuário autenticado.
```csharp
if(userName=="Admin" && password == "password"){  
  
                //Create the identity for the user  
                var identity = new ClaimsIdentity(new[] {  
                    new Claim(ClaimTypes.Name, userName)  
                }, CookieAuthenticationDefaults.AuthenticationScheme);  
  
                var principal = new ClaimsPrincipal(identity);  
  
                var login = HttpContext.SignInAsync(CookieAuthenticationDefaults.AuthenticationScheme, principal);  
  
                return RedirectToAction("Index", "Home");  
            }  
  
```

Com estes dados preenchidos, você pode acessa-lo pelo @User.Identity através do Razor.

Para fazer Logoff, crie o controller:
```csharp
[HttpPost]  
public IActionResult Logout()  
{  
      var login = HttpContext.SignOutAsync(CookieAuthenticationDefaults.AuthenticationScheme);  
      return RedirectToAction("Login");  
}  
```

Artigo resumido do site:
https://www.c-sharpcorner.com/article/authentication-and-authorization-in-asp-net-core-mvc-using-cookie/
