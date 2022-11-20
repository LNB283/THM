# Neighbour Walkthrough
### Link
- https://tryhackme.com/room/neighbour
---
### Question: Find the flag on your neighbor's logged in page!

When we try to access the targeted machine through our browser, we reach a login page. 

Before to start something like brute force attack, let's check the **source code** of this login page.

We found a really important information in comments ^^
```
<p>Don't have an account? Use the guest account! (<code>Ctrl+U</code>)</p>
            <!-- use guest:guest credentials until registration is fixed. "admin" user account is off limits!!!!! -->
        </form>
```

- First: Try **guest:guest** as login:password. We are logged and if we check the **URL**, we catch an important information related to the room synopsis (Check out similar content : **IDOR**)
```
http://[IP]/profile.php?user=guest
```
- Second: Change **guest** with **admin** and reload the page and we found the flag ^^

### Answer: flag{66be95c478473d91a5358f2440c7af1f}