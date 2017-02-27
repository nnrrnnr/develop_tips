# develop_tips

## Slackにサーバーリクエストログを投稿する with PHP
```php
<?php

class SlackBotInfo
{
    public $channel = "#[channel]";
    public $username = "[user_name]";
    public $icon_emoji = ':[emoji]:';
    protected $message = '';
    private $url = '[hooks.slack.com]';

    public function __construct($message = '')
    {
        $this->set_message($message);
    }

    public function set_message($message)
    {
        $this->message = $message;
    }

    public function get_post_info()
    {
        return array(
            'url' => $this->url,
            'body' => array(
                'payload' => json_encode(array(
                    'channel' => $this->channnel,
                    'username' => $this->username,
                    'icon_emoji' => $this->icon_emoji,
                    'text' => $this->message,
                )),
            ),
        );
    }
}

```
```php
<?php

class SlackBot
{
    protected function create_option($info)
    {
        return array(
            CURLOPT_URL => $info['url'],
            CURLOPT_POST => true,
            CURLOPT_POSTFIELDS => $info['body'],
            CURLOPT_RETURNTRANSFER => true,
            CURLOPT_HEADER => true,
        );
    }

    protected function request($options)
    {
        $ch = curl_init();
        curl_setopt_array($ch, $options);
        $result = curl_exec($ch);
        $header_size = curl_getinfo($ch, CURLINFO_HEADER_SIZE);
        $header =  substr($result, 0, $header_size);
        $result = substr($result, $header_size);
        curl_close($info);

        return array(
            'Header' => $header,
            'Result' => $result,
        );
    }

    public function post_message($info)
    {
        return $this->request($this->create_option($info->get_post_info()));
    }
}
```
