---
title: ["[Webhacking.kr] Better than 𝒳ℳℒ (nosql - 200)"]
date: 2024-12-01 09:00:00 +0900
categories: [writeup, webhacking.kr]
tags: [web, unsolved]
---

```php
<?php
include "../../config.php";
$db = dbconnect();
if($_GET['page'] == "logout"){
  session_destroy();
  exit("<script>location.href='./';</script>");
}
if($_SESSION['better_than_xml_id']){
  echo "Hello {$_SESSION['better_than_xml_id']}!<hr>";
  if($_SESSION['better_than_xml_role'] === "admin"){
    mysqli_query($db,"DELETE FROM challn17"); // clear table
    unset($_SESSION['better_than_xml_role']);
    unset($_SESSION['better_than_xml_id']);
    solve(80);
  }
  else echo "Your role is [{$_SESSION['better_than_xml_role']}]";
  echo "<hr><a href=./?page=logout>logout</a>";
  exit;
}
if($_GET['page'] == "join_"){
  $userInput = file_get_contents("php://input");
  $json = json_decode($userInput,true);
  foreach($json as $key => $value){
    if(($key !== "userid") && ($key !== "userpw") && ($key !== "role")) exit("invalid input");
    if(gettype($value) != "string") exit("no hack ~_~");
  }
  if(strlen($json['userid']) < 5) exit("too short id");
  if(strlen($json['userpw']) < 5) exit("too short pw");
  if($json['role'] !== "guest") exit("no hack ~_~");
  $stmt = $db->prepare("INSERT INTO challn17 values(?)");
  $stmt->bind_param("s", $userInput);
  $stmt->execute();
  echo "done";
  exit;
}
elseif(($_POST['uid']) && ($_POST['upw']) && ($_GET['page'] == "login_")){
  $query = "SELECT JSON_UNQUOTE(JSON_EXTRACT(data,'$.role')) as role FROM challn17 WHERE JSON_UNQUOTE(JSON_EXTRACT(data,'$.userid'))=? and JSON_UNQUOTE(JSON_EXTRACT(data,'$.userpw'))=?";
  $stmt = $db->prepare($query);
  $stmt->bind_param("ss", $_POST['uid'], $_POST['upw']);
  $stmt->execute();
  $result = $stmt->get_result();
  $result = $result->fetch_assoc();
  if($result['role']){
    $_SESSION['better_than_xml_id'] = $_POST['uid'];
    $_SESSION['better_than_xml_role'] = $result['role'];
    exit("<script>location.href='./';</script>");
  }
  else{
    exit("login failed..");
  }
}
elseif($_GET['page'] == "join"){
  echo <<<joinform
<script src=join.js></script>
<h2>Join</h2>
<form onsubmit="join();return false">
userid : <input id=uid><br>
userpw : <input id=upw><br>
<input type=submit><br>
<a href=?page=login>login</a>
joinform;
}
else{
  echo <<<loginform
<h2>Login</h2>
<form method=post action=?page=login_>
userid : <input name=uid><br>
userpw : <input name=upw><br>
<input type=submit><br>
<a href=?page=join>join</a>
loginform;
}
echo "<hr>";
highlight_file(__FILE__);
?>
```


