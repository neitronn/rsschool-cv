## Summary 

Arzin Igor Vladimirovich
 
**Date of birth:** *18.02.1990* 

**Phone:** *+375 (29) 191-75-64* **Email:** *neitron_h19@mail.ru*

**Goal:** to build a career in the IT sector. 
I am honest, responsible, stress-resistant, able to work in a team,
able to solve problems, ready to learn and constantly engaged in self-education . I would like to realize myself in the field of IT.

**Skills:**

+ HTML5, CSS3
+ PHP
+ Pascal
+ Delphi
+ C#
+ C++
+ MS SQL Server

**Code Examples**

~~~~
<?php
defined('ZASHITA') or die('Access denied');


function gild(){
	$query = "SELECT * FROM gild";
	global $link;
	$res = mysqli_query($link, $query);
	
	$gildii = array();
	while ($row = mysqli_fetch_assoc($res)) {
		$gildii[] = $row;
	}
	return $gildii;
}


function menu_g($id_g, $id_m){
	$query = "SELECT * FROM menu_gild WHERE `id_menu`='$id_m' AND `id_gild`='$id_g'";
	global $link;
	$res = mysqli_query($link, $query) or die(mysql_query());
	if (mysqli_num_rows($res) == 1) {
		return 1;
	} else {
		return 0;
	}
}

function shablon($g){
    global $link;
	$g=mysqli_real_escape_string($link, $g);
	$query = "SELECT * FROM gild WHERE `krot_mane`='$g'";
	
	$res = mysqli_query($link, $query) or die(mysql_query());
	
	if (mysqli_num_rows($res) == 1) {
		$row = mysqli_fetch_assoc($res);
		return $row;
	} else {
		return 0;
	}
	
}


function addip(){
   $client  = @$_SERVER['HTTP_CLIENT_IP'];
	$forward = @$_SERVER['HTTP_X_FORWARDED_FOR'];
	$remote  = @$_SERVER['REMOTE_ADDR'];
 
if(filter_var($client, FILTER_VALIDATE_IP)) $ip = $client;
elseif(filter_var($forward, FILTER_VALIDATE_IP)) $ip = $forward;
else $ip = $remote;
$ip = shifr_ip($ip);
	$d  = date("j.n.Y");
	$a = $_SERVER["HTTP_REFERER"];
    global $link;
	 if (count_table_par('users_ip','ip',$ip,'date',$d)==0)	{
    $query = "INSERT INTO users_ip (ip, date, addres ) VALUES ('$ip','$d','$a')";
			 $res = mysqli_query($link, $query); 
	 }
}


function registration ($id_gild){
	$error = '';
	global $link;
	$login=mysqli_real_escape_string($link,trim($_POST['login']));
	$login_game=mysqli_real_escape_string($link,trim($_POST['login_game']));
	$mail=mysqli_real_escape_string($link,trim($_POST['email']));  
	$nastroi=mysqli_real_escape_string($link,trim($_POST['nastroi']));
	$id_gild = (int) $id_gild;
	$pass = trim($_POST['pass']);
	$pass2 = trim($_POST['pass2']);
	$date =  date("j.n.Y");
	$sailt=sailt();
	
	//Полкчаем ip адрес
	$client  = @$_SERVER['HTTP_CLIENT_IP'];
	$forward = @$_SERVER['HTTP_X_FORWARDED_FOR'];
	$remote  = @$_SERVER['REMOTE_ADDR'];
 
if(filter_var($client, FILTER_VALIDATE_IP)) $ip = $client;
elseif(filter_var($forward, FILTER_VALIDATE_IP)) $ip = $forward;
else $ip = $remote;
$ip = shifr_ip($ip);


	if (empty($login)) { $error .= '1'; $_SESSION['reg']['res']['login'] = 'Не указан логин';}
	if (empty($pass)) { $error .= '2';  $_SESSION['reg']['res']['pass'] = 'Не указан Пароль';}
	if (empty($mail)) { $error .= '3';  $_SESSION['reg']['res']['mail'] = 'Не указан Email';}
	if ($pass != $pass2) { $error .= '4';  $_SESSION['reg']['res']['pass2'] = 'Несовподают пароли';}
	
	if (empty($error)) {
		$query = "SELECT id FROM users WHERE login = '$login' LIMIT 1";
		$res = mysqli_query($link,$query) ;
		$row = mysqli_num_rows($res);
		if ($row == 1 ){
			//есть такой пользователь
			$_SESSION['reg']['res'] = "5";
		}else{
			
			$query = "SELECT id FROM users WHERE mail = '$mail' LIMIT 1";
			$res = mysqli_query($link,$query) ;
			$row = mysqli_num_rows($res);
			if ($row == 1 ){
				$_SESSION['reg']['res'] = "6";
			} else {
			
			
				//нет такова пользователя
				$pass = hesh_pas($pass,$sailt);
				$pass = password_hash($pass, PASSWORD_DEFAULT);
			$login_game = $login;
			$mail = shifr_mail($mail);
				$query = "INSERT INTO users (id_gild, login, login_game, pas, sailt, mail, ip, date)
				VALUES ($id_gild, '$login', '$login_game', '$pass', '$sailt', '$mail', '$ip', '$date')";
				$res = mysqli_query($link,$query);
				if (mysqli_affected_rows($link)>0){
					$_SESSION['reg']['res'] = "8";
					$_SESSION['auth']['user'] = $login;
				}else{$_SESSION['reg']['res'] = "7";}
				
			}
		}
		
	}else{
		$_SESSION['reg']['res'] = "-1";
	
	}
	
 return $_SESSION['reg']['res'];
	
}


function autorization($au_log,$au_pas){
	global $link;
	$login=mysqli_real_escape_string($link,trim($au_log));
	$pass = trim($au_pas);
	
	
	if (empty($login) OR empty($pass)) {
		return "0"; //Незаполнин логин и/или пароль
		}else{
			
			$query = "SELECT login,sailt,pas FROM users WHERE login = '$login'  LIMIT 1";
			$res =  mysqli_query($link,$query) ;
			if (mysqli_num_rows($res) == 1){ //логин найден
				$row = mysqli_fetch_assoc($res);
				
				$pass_h = hesh_pas($pass,$row['sailt']);
				if (password_verify($pass_h, $row['pas'])) {
					$_SESSION['auth']['user'] = $row['login']; 
					return "3";
				}else{
					return "2"; //пароль не совпал
				} 
			}else {
				return "1"; //Неверный логтн и/или пароль
				
			}
		}
		
	
	
}


function sailt(){
    $sailt = "";
    for ($i=1; $i<=20; $i++)
    {
     $sailt .=chr(rand(33,126));   
    }
    return $sailt;
}

function hesh_pas($pas,$sa){ 
  $pas .= $sa;
    $pas = md5(md5($sa).md5($pas));
    
    return $pas;
}

function shifr_mail($mail){
   $mail = strrev($mail);
   $re = "";
    for($i=0;$i<strlen($mail);$i++)
    {
      $re .= chr(ord($mail[$i]) - $i); 
    }
    return $re;
}


function count_table($name){ 
global $link;
	
	
	$a = mysqli_query($link, "SELECT COUNT(1) FROM $name");
	$b = mysqli_fetch_array( $a );
	return $b[0]; 
}

function count_table_par($name,$pole1,$znach1,$pole2,$znach2){ 
global $link;
	
	
	$a = mysqli_query($link, "SELECT COUNT(1) FROM $name WHERE $pole1 = '$znach1' AND $pole2 = '$znach2' ");
	$b = mysqli_fetch_array( $a );
	return $b[0]; 
}


function count_table2($name,$pole1,$znach1){ 
global $link;
	
	
	$a = mysqli_query($link, "SELECT COUNT(1) FROM $name WHERE $pole1 = '$znach1' ");
	$b = mysqli_fetch_array( $a );
	return $b[0]; 
}


?>
~~~~

**Education:**

+ 2007-2010 "Gomel State Machine-Building College" . Specialty: "information technology Software".
+ 2011-2017 Francisk Skorina Gomel State University.Specialty: Automated information processing systems.
English-basic.