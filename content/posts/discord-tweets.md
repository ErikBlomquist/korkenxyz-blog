## Twitter Notifications on Discord

- [Discord](#discord)
- [IFTTT](#ifttt)

### Discord
1. Create a new discord text channel on your server by clicking the plus sign close to your server name. 

    ![step1](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/tweet-discord1.jpeg "Create new text channel")

2. Make sure text channel is selected and give it a name and then click **Create channel**.

    ![step2](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/tweet-discord2.jpeg "Create new text channel")

3. Create a webhook for that channel by clicking the settings wheel of your new text channel and then webhooks. Click **Create Webhook**

    ![step3](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/tweet-discord3.jpeg "Create new webhook")

4. Scroll down to the bottom and copy the webhook url and then **Save**.

    ![step4](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/tweet-discord4.jpeg "Get webhook url")

### IFTTT
1. Create an account @ https://www.ifttt.com and login.
2. Once logged in, click **Get more** in the top right corner.
![ifft-step-1](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/ifft-1.png)

3. Search for twitter in the search field and then click **Services** and then **Twitter**.
![ifft-step-2](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/ifft-2.png)

4. Click **Connect** and then **Authorize app**.
5. Click on your profile picture in the top left corner and then **Create**.
![ifft-step-3](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/ifft-3.png)

6. Click on the big black and white plus sign between **IF** and **THEN**.
![ifft-step-4](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/ifft-4.png)


7. Scroll down/Search and select the **Twitter Icon**.
![ifft-step-5](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/ifft-5.png)

8. Select **New tweet by a specific user** and input the name of the user you want to get notifications from. 
![ifft-step-6](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/ifft-6.png)

9. Click **Create trigger**.
![ifft-step-7](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/ifft-7.png)

10. Click on the big black and white plus sign between **THEN** and **THAT**.
![ifft-step-8](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/ifft-8.png)

11. Search and select **Webhooks** and then click **Connect**.
![ifft-step-9](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/ifft-9.png)

12. Select **Make a web request** as the action.
![ifft-step-10](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/ifft-10.png)


13. Paste the webhook you created earlier into the **URL**
14. For **Method** select **POST**.
15. **Content Type** select **application/json**.
16. For the code below, replace @username with the handle you are getting notifications from and paste the code into the **Body**.
    ```
    { "username":"@username just tweeted", "content":"@{{UserName}} tweeted this at {{CreatedAt}}: {{LinkToTweet}}" }
    ```

17. Click **Create Action**.
![ifft-step-11](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/ifft-11.png)

18. Disable **Recieve notifications when this Applets runs** and then click **Finish**.
19. You're done! You will now be notified on discord when you (or the twitter handle you picked) posts a new tweet.


