# NNsns_alba_main
Some N Some Alba admin page

<!DOCTYPE html>
<?php
$conn = mysqli_connect('49.1.244.132','sometalk','sometalk79','sometalk') or die ("Failed");

$query = "select distinct id from tb_alba order by id";
$query2 = "select distinct name from tb_alba order by name";

$queryAll = "select b.name as name, a.id as id, b.worktime as worktime,
		count(DISTINCT a.peer_id)as chat_male, SUM(if(a.kind='send',1,null))as send_cnt,
		SUM(if(a.kind='receive',1,null))as receive_cnt, count(a.kind)as total_cnt,
		sum(if(length(a.content) <= 20 and a.content REGEXP '안녕|하이|ㅎㅇ',1,null)) as hi_cnt,
		min(from_unixtime(a.wtime))as uindate, max(from_unixtime(a.wtime))as uoutdate, c.memo as memo
		from tb_chat as a INNER JOIN tb_alba as b on a.id = b.id INNER JOIN tb_user as c on b.id = c.id";

// $queryName = "select id, count(DISTINCT peer_id)as chat_male, SUM(if(kind='send',1,null))as send_cnt,
// 		SUM(if(kind='receive',1,null))as receive_cnt, count(kind)as total_cnt,
// 		sum(if(length(content) <= 20 and content REGEXP '안녕|하이|ㅎㅇ',1,null)) as hi_cnt,
// 		min(from_unixtime(wtime))as uindate, max(from_unixtime(wtime))as uoutdate from tb_chat";

// $queryAll = "select id, count(DISTINCT peer_id)as chat_male, SUM(if(kind='send',1,null))as send_cnt,
// 		SUM(if(kind='receive',1,null))as receive_cnt, count(kind)as total_cnt,
// 		sum(if(length(content) <= 20 and content REGEXP '안녕|하이|ㅎㅇ',1,null)) as hi_cnt,
// 		min(from_unixtime(wtime))as uindate, max(from_unixtime(wtime))as uoutdate
// 		from tb_chat where id in (select id from tb_user where memo like '%조실장%')";

$queryEnd = " GROUP BY a.id having send_cnt > 0 ORDER BY a.id";

$result = mysqli_query($conn, $query);
$result2 = mysqli_query($conn, $query2);


	if (mysqli_connect_errno($conn)) {
		echo 'Failed</br>';
   	} else { echo 'MySQL D/B Connect Sucess</br>';}


function print_out2($arg) {
	?>
		<tr>
			<td> <?=$arg['name']?> </td>
			<td> <?=$arg['id']?> </td>
			<td> <?=$arg['worktime']?> </td>
			<td> <?=$arg['chat_male']?> 명 </td>
			<td> <?=$arg['send_cnt']?> 회 </td>
			<td> <?=$arg['receive_cnt']?> 회 </td>
			<td> <?=$arg['total_cnt']?> 회 </td>
			<td> <?=$arg['hi_cnt']?> 회 </td>
			<td> <?=$arg['uindate']?> </td>
			<td> <?=$arg['uoutdate']?> </td>
			<td> <?=$arg['memo']?> </td>
<!-- 			<br /> -->
		</tr>
	<?php
}

?>
<html>
<head>
	<meta charset="utf-8" />
	<style type="text/css">
            body {
                font-size: 0.8em;
                font-family: dotum;
                line-height: 1.6em;
            }
            header {
                border-bottom: 1px solid #ccc;
                padding: 20px 0;
            }
            nav {
                float: left;
                margin-right: 20px;
                min-height: 1000px;
                min-width:150px;
                border-right: 1px solid #ccc;
            }
            nav ol {
                list-style: none;
                padding-left: 0;
                padding-right: 20px;
            }
            article {
                float: left;
                /*width: 200;*/
            }
            #menu > li {
            	border-bottom: 1px solid gray;
            	margin-top: 1px;
            	text-decoration: none;
            }
            #column {
            	background-color: gray;
            	font-weight: bold;
	/*			padding: 10px;*/
            }
            table {
	            border-collapse: collapse;
	            border:1px solid red;
	            padding: 10px;
			}
            table td {
	            border-collapse: collapse;
	            border:1px solid red;
	            padding: 10px;
			}

        </style>
</head>
<body>
	<nav>
		<ol id='menu'>
			<li><a href="/indexsns2.php">HOME</a></li>
			---------------------<br />
			<li><a href="http://localhost:8090/indexsns2.php?id=all">알바 전체보기</a></li>
			---------------------<br />
			아이디별 목록 보기
			<?php
            	while($row = mysqli_fetch_assoc($result)) {
                	echo "<li><a href=\"?id={$row['id']}\">".htmlspecialchars($row['id'])."</a></li>";
                }

			?>
            
            ---------------------<br />
			이름별 목록 보기
			<?php
			    while($row = mysqli_fetch_assoc($result2)) {
                	echo "<li><a href=\"?name={$row['name']}\">".htmlspecialchars($row['name'])."</a></li>";
                }
			?>
		</ol>
	</nav>
	<article>
		<h1>어플 알바 페이지</h1>
		<br />	

<div>
	<?php
	// if(!empty($_GET['id']) or !empty($_GET['name'])) {
		//$resultInfo = mysqli_query($conn, $query." and id='".$_GET['id']."'");
		if (!empty($_GET['id']) and empty($_GET['name'])) {
			$result_info = mysqli_query($conn, $queryAll." where a.id = '".$_GET['id']."'  GROUP BY a.id");
			while ($row_alba = mysqli_fetch_assoc($result_info)) {
	?>
			<tr>
				<h3>
				<td> 아이디 : <?=$row_alba['id']?> </td> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
				<td> 대화명 : <?=$row_alba['name']?> </td> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
				<td> 알바 시간 : <?=$row_alba['worktime']?> </td> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
				<td> 메모 : <?=$row_alba['memo']?> </td> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

				<br />
	--------------------------------------------------------------------------------------------------------------------------------------
				</h3>
			</tr>
	<?php
			}
		} else if (empty($_GET['id']) and !empty($_GET['name'])) {
			$result_info = mysqli_query($conn, $queryAll." where b.name = '".$_GET['name']."'  GROUP BY b.name");
			while ($row_alba = mysqli_fetch_assoc($result_info)) {
	?>
			<tr>
				<h3>
				<td> 아이디 : <?=$row_alba['id']?> </td> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
				<td> 대화명 : <?=$row_alba['name']?> </td> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
				<td> 알바 시간 : <?=$row_alba['worktime']?> </td> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
				<td> 메모 : <?=$row_alba['memo']?> </td> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

				<br />
	--------------------------------------------------------------------------------------------------------------------------------------
				</h3>
			</tr>
	<?php
			}
		}
	// }
	?>
</div>




<?php
    if (!empty($_GET['id'])) {
?>
<h3>날짜별 검색</h3>
	<p><form action="http://localhost:8090/indexsns2.php" method="get">
		<!-- <select name = 'uindate'> -->
		<select name = 'uindate' style="width:150px; border:2px solid #353535; font:12px malgun gothic;"> 
			<option value = "days" selected>전체보기</option>
			<option value = "today">오늘</option>
			<option value = 'yesterday'>어제</option>
			<option value = 'byesterday'>그저께</option>
            <input type="hidden" name="id" value=<?=$_GET['id']?>>
		<!-- <option value="<?=$rows[price];?>"><?=$rows[goods];?></option> -->
		</select>
			<input type = 'submit' value="검색">
	</p></form>
<?php
    }
    if (!empty($_GET['name'])) {
?>
<h3>날짜별 검색</h3>
	<p><form action="http://localhost:8090/indexsns2.php" method="get">
		<!-- <select name = 'uindate'> -->
		<select name = 'uindate' style="width:150px; border:2px solid #353535; font:12px malgun gothic;"> 
			<option value = "days" selected>전체보기</option>
			<option value = "today">오늘</option>
			<option value = 'yesterday'>어제</option>
			<option value = 'byesterday'>그저께</option>
            <input type="hidden" name="name" value=<?=$_GET['name']?>>
		<!-- <option value="<?=$rows[price];?>"><?=$rows[goods];?></option> -->
		</select>
			<input type = 'submit' value="검색">
	</p></form>
<?php
    }
?>
<!-- <h3>날짜별 상세 검색</h3>
	<p><form action="http://localhost:8090/indexsns2.php" method="get">
        <input type="text" name="usdate" value="S" style="width:150px; border:2px solid #353535; font:12px malgun gothic;">
		<input type="text" name="uedate" value="E" style="width:150px; border:2px solid #353535; font:12px malgun gothic;">
		<input type="hidden" name="id" value=<?=$_GET['id']?>>
		<input type = 'submit' value="상세검색">
	</p></form> -->

	<table border = "1" width="100%">
	<tr id="column">
		<th> 이름 </th>
		<th> 아이디 </th>
		<th> 알바 시간 </th>				
		<th> 채팅한 회원 수 </th>
		<th> 보낸 메세지 횟수 </th>
		<th> 받은 메세지 횟수 </th>
		<th> 총 메세지 횟수 </th>
		<th> 인사만 보낸 횟수 </th>
		<th> 시작시간 </th>
		<th> 종료시간 </th>
		<th> 메모 </th>
	</tr>



    <?php
    	if (!empty($_GET['id']) or !empty($_GET['name'])) {
    		echo "DATA HAVE!<BR>";

    		if (!empty($_GET['id']) and empty($_GET['name'])) {

				if (($_GET['id']) != 'all' and empty($_GET['uindate'])) {
					$result_id = mysqli_query($conn, $queryAll." where a.id = '".$_GET['id']."'");
					echo "아이디별 개별보기 성공!<BR><BR>";
					while ($row_id = mysqli_fetch_assoc($result_id)) {
						print_out2($row_id);
					}
					
				} else if (($_GET['id']) != 'all' and !empty($_GET['uindate'])) {
					switch($_GET['uindate']){
		    			case 'today':
		        			$result_id = mysqli_query($conn, $queryAll." where a.id = '".($_GET['id'])."' and from_unixtime(a.wtime) >= CURDATE()".$queryEnd);
		        			break;
		    			case 'yesterday':
		        			$result_id = mysqli_query($conn, $queryAll." where a.id = '".($_GET['id'])."' and from_unixtime(a.wtime) >= DATE_ADD(curdate(),INTERVAL -1 day) and from_unixtime(a.wtime) < curdate()".$queryEnd);
		        			break;
		    			case 'byesterday':
		        			$result_id = mysqli_query($conn, $queryAll." where a.id = '".($_GET['id'])."' and from_unixtime(a.wtime) >= DATE_ADD(curdate(),INTERVAL -2 day) and from_unixtime(a.wtime) < DATE_ADD(curdate(),INTERVAL -1 day)".$queryEnd);
		        			break;
		        		case 'days':
		        			$result_id = mysqli_query($conn, $queryAll." where a.id = '".$_GET['id']."'");
		        			break;
	 				}
						echo "아이디별 날짜 검색보기 성공!<br><BR>";
						while ($row_id = mysqli_fetch_assoc($result_id)) {
							print_out2($row_id);
						}

		    	} else if (($_GET['id']) == 'all' and !empty($_GET['uindate'])) {
					switch($_GET['uindate']){
		    			case 'today':
		        			$result_all = mysqli_query($conn, $queryAll." where from_unixtime(a.wtime) >= CURDATE()".$queryEnd);
		        			break;
		    			case 'yesterday':
		        			$result_all = mysqli_query($conn, $queryAll." where from_unixtime(a.wtime) >= DATE_ADD(curdate(),INTERVAL -1 day) and from_unixtime(a.wtime) < curdate()".$queryEnd);
		        			break;
		    			case 'byesterday':
		        			$result_all = mysqli_query($conn, $queryAll." where from_unixtime(a.wtime) >= DATE_ADD(curdate(),INTERVAL -2 day) and from_unixtime(a.wtime) < DATE_ADD(curdate(),INTERVAL -1 day)".$queryEnd);
		        			break;
						case 'days':
		        			$result_all = mysqli_query($conn, $queryAll.$queryEnd);
		        			break;
	 				}	    		
					echo "전체 + 날짜 검색보기 성공!<BR><BR>";
					while ($row_all = mysqli_fetch_assoc($result_all)) {
						print_out2($row_all);
					}
				
		    	} else {
					$result_all = mysqli_query($conn, $queryAll.$queryEnd);
					echo "전체보기 성공!<BR><BR>";
					while ($row_all = mysqli_fetch_assoc($result_all)) {
						print_out2($row_all);
					}
                }
			} else if (empty($_GET['id']) and !empty($_GET['name'])) {

				if (!empty($_GET['name']) and empty($_GET['uindate'])) {
					$result_name = mysqli_query($conn, $queryAll." where b.name = '".$_GET['name']."'".$queryEnd);
					echo "이름별 개별보기 성공!<BR><BR>";
					while ($row_name = mysqli_fetch_assoc($result_name)) {
						print_out2($row_name);
					}
					
				} else if (!empty($_GET['name']) and !empty($_GET['uindate'])) {
					switch($_GET['uindate']){
		    			case 'today':
		        			$result_name = mysqli_query($conn, $queryAll." where b.name = '".($_GET['name'])."' and from_unixtime(a.wtime) >= CURDATE()".$queryEnd);
		        			break;
		    			case 'yesterday':
		        			$result_name = mysqli_query($conn, $queryAll." where b.name = '".($_GET['name'])."' and from_unixtime(a.wtime) >= DATE_ADD(curdate(),INTERVAL -1 day) and from_unixtime(a.wtime) < curdate()".$queryEnd);
		        			break;
		    			case 'byesterday':
		        			$result_name = mysqli_query($conn, $queryAll." where b.name = '".($_GET['name'])."' and from_unixtime(a.wtime) >= DATE_ADD(curdate(),INTERVAL -2 day) and from_unixtime(a.wtime) < DATE_ADD(curdate(),INTERVAL -1 day)".$queryEnd);
		        			break;
	 				}
						echo "검색보기 성공!<br><BR>";
						while ($row_name = mysqli_fetch_assoc($result_name)) {
							print_out2($row_name);
						}
				}
			}
	
    	} else {
    		echo "<h1>초기 화면이다!</h1>";	
    	}
    ?>


	</table>
	</article>
</body>
</html>

<!-- 
	        $result = mysql_query("INSERT INTO topic (title, description, created) VALUES ('".mysql_real_escape_string($_POST['title'])."', '".mysql_real_escape_string($_POST['description'])."', now())");
	        header("Location: list.php");  -->


<!-- 
select min(tb_alba.`name`) as `name` , tb_chat.id, tb_alba.worktime as worktime,
count(DISTINCT tb_chat.peer_id)as chat_male, SUM(if(tb_chat.kind='send',1,null))as send_cnt,
SUM(if(tb_chat.kind='receive',1,null))as receive_cnt, count(tb_chat.kind)as total_cnt,
sum(if(length(tb_chat.content) <= 20 and tb_chat.content REGEXP '안녕|하이|ㅎㅇ',1,null)) as hi_cnt,
min(from_unixtime(tb_chat.wtime))as uindate, max(from_unixtime(tb_chat.wtime))as uoutdate 
from tb_chat as tb_chat INNER JOIN tb_alba as tb_alba on tb_chat.id = tb_alba.id 
group by tb_chat.id
having send_cnt > 0;

 -->

<!-- 

$conn = mysqli_connect('49.1.244.132','sometalk','sometalk79','sometalk') or die ("Failed");
//$query = "select distinct id from tb_chat where id in (select id from tb_alba)";
$query = "select distinct id from tb_alba order by id";
//$queryJozzzz = "select id from tb_user where memo like '%조실장%'"
$query2 = "select distinct name from tb_alba order by name";

$queryS = "select id, count(DISTINCT peer_id)as chat_male, SUM(if(kind='send',1,null))as send_cnt,
		SUM(if(kind='receive',1,null))as receive_cnt, count(kind)as total_cnt,
		sum(if(length(content) <= 20 and content REGEXP '안녕|하이|ㅎㅇ',1,null)) as hi_cnt,
		min(from_unixtime(wtime))as uindate, max(from_unixtime(wtime))as uoutdate from tb_chat";
$query_all = "select id, count(DISTINCT peer_id)as chat_male, SUM(if(kind='send',1,null))as send_cnt,
		SUM(if(kind='receive',1,null))as receive_cnt, count(kind)as total_cnt,
		sum(if(length(content) <= 20 and content REGEXP '안녕|하이|ㅎㅇ',1,null)) as hi_cnt,
		min(from_unixtime(wtime))as uindate, max(from_unixtime(wtime))as uoutdate
		from tb_chat where id in (select id from tb_user where memo like '%조실장%')";
$query_order = " GROUP BY id order by id";

 -->


