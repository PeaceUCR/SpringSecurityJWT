JSON Web Token GetStart
SpringBoot + SpringSecurity + JWT + MongoDB

Challenge here:
(login then generate token, access with token, filter resolve info from token then check and grant auth)
How to add a filter to resolve the token and transfer to auth  
https://stackoverflow.com/questions/42354138/spring-security-token-based-authentication

GenericFilterBean vs Filter???  
JwtTokenFilterConfigurer extends SecurityConfigurerAdapter<DefaultSecurityFilterChain, HttpSecurity>???  
http.apply(new JwtTokenFilterConfigurer(jwtTokenProvider)); ????

Why this statement? How it works?
  


# How to Use

![alt text](https://github.com/PeaceUCR/SpringSecurityJWT/blob/master/signup.png?raw=true)
![alt text](https://github.com/PeaceUCR/SpringSecurityJWT/blob/master/signin.png?raw=true)
![alt text](https://github.com/PeaceUCR/SpringSecurityJWT/blob/master/view.png?raw=true)


# /security/JwtTokenFilter

The JwtTokenFilter filter is applied to each API (/**) with exception of the signin token endpoint (/users/signin) and singup endpoint (/users/signup).

This filter has the following responsibilities:

1.Check for access token in Authorization header. If Access token is found in the header, delegate authentication to JwtTokenProvider otherwise throw authentication exception

2.Invokes success or failure strategies based on the outcome of authentication process performed by JwtTokenProvider
Please ensure that chain.doFilter(request, response) is invoked upon successful authentication. You want processing of the request to advance to the next filter, because very last one filter FilterSecurityInterceptor#doFilter is responsible to actually invoke method in your controller that is handling requested API resource.
![alt text](https://github.com/PeaceUCR/SpringSecurityJWT/blob/master/JwtTokenFilter.png?raw=true)

# /security/JwtTokenFilterConfigurer

Adds the JwtTokenFilter to the DefaultSecurityFilterChain of spring boot security.
https://medium.com/@xoor/jwt-authentication-service-44658409e12c

# /security/JwtTokenProvider
The JwtTokenProvider has the following responsibilities:

1.Verify the access token's signature
2.Extract identity and authorization claims from Access token and use them to create UserContext
3.If Access token is malformed, expired or simply if token is not signed with the appropriate signing key Authentication exception will be thrown

# /security/MyUserDetails
Implements UserDetailsService in order to define our own custom loadUserbyUsername function. The UserDetailsService interface is used to retrieve user-related data. It has one method named loadUserByUsername() which finds a user entity based on the username and can be overridden to customize the process of finding the user.

It is used by the DaoAuthenticationProvider to load details about the user during authentication.


# /config/WebSecurityConfig

The WebSecurityConfig class extends WebSecurityConfigurerAdapter to provide custom security configuration.

Following beans are configured and instantiated in this class:

JwtTokenFilter

PasswordEncoder

Also, inside WebSecurityConfig#configure(HttpSecurity http) method we'll configure patterns to define protected/unprotected API endpoints. Please note that we have disabled CSRF protection because we are not using Cookies.

REF:  
https://medium.com/@xoor/jwt-authentication-service-44658409e12c  
https://github.com/murraco/spring-boot-jwt
