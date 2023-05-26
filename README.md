# aws-beanstalk

Deploy Calculator App in Beanstalk
==================================


1. Launch an EC2 (Ubuntu 22.04)
  sudo hostnamectl set-hostname calculator
  sudo apt update
  sudo apt install maven wget unzip -y
  mvn --version

  sudo apt  install awscli
  aws configure
  Enter AKID, SAK

  # test if config is proper
  aws s3 ls

 
2. Upload the code for the simple calculator app to S3. (only first time)
# To tare the directory:
 tar -zcvf calculator.tar.gz calculator-app

# download the tar file to laptop
 scp -i unus-kb2-keypair.pem ubuntu@ec2-54-236-49-88.compute-1.amazonaws.com:~/calculator.tar.gz ./

3. Download the code for the simple calculator app from S3

 # Download the Java code we are going to use in Application
 wget https://s3.amazonaws.com/klowdbay.com/05.+CodeResources/calculator.tar.gz

 tar -xzvf calculator.tar.gz

 ls calculator-app

 ls calculator-app/src/main/webapp

 cat calculator-app/src/main/webapp/index.jsp

### start of code ###
<html>
 <head>
 <title>calculator program in jsp</title>
 <script>
   function checkValue(){
   var msg = "";
   if (f1.operand1.value == "" || f1.operand2.value == "")
     msg += "Operands missing\n";
   if (f1.op.selectedIndex == 3 && f1.operand2.value == 0)
     msg += "Division by zero";
   if (msg.length > 0){
     alert(msg);
     return false;
   } else
     return true;
   }
 </script>
</head>

<body style="background-color:powderblue;">

 <%
  String str = "0",op2 = "0";
  int result = 0;
  String op = "+";
  char opchar = op.charAt(0);
  if (request.getParameter("op") != null){
    op = request.getParameter("op");
    opchar = op.charAt(0);
    str = request.getParameter("operand1");
    op2 = request.getParameter("operand2");
    switch(opchar){
      case '0': result = Integer.parseInt(str) + Integer.parseInt(op2);
      break;
      case '1': result = Integer.parseInt(str) - Integer.parseInt(op2);
      break;
      case '2': result = Integer.parseInt(str) * Integer.parseInt(op2);
      break;
      case '3': result = Integer.parseInt(str) / Integer.parseInt(op2);
      break;
    }
  }
%>
<center>

 <h2>Simple Calculator Program </h2>
 <form method ="get" name ="f1" onsubmit = "return checkValue()">
 <input type ="text" size ="20" name ="operand1" value = <%= str %> />

 <select name = op size = 1>
 <option value = "0" <% if (opchar == '0') out.print("selected"); %> >+</option>
 <option value = "1" <% if (opchar == '1') out.print("selected"); %> >-</option>
 <option value = "2" <% if (opchar == '2') out.print("selected"); %> >*</option>
 <option value = "3" <% if (opchar == '3') out.print("selected"); %> >/</option>

</select>

 <input type ="text" size="20" name ="operand2" value = <%= op2 %> />
 <p>
 <input type = submit value = Calculate />

 Result = <%= result + "" %>
</form>

</body>

</html>

### end of code ###


4. Build the load using maven
   cd calculator-app
   mvn clean package

5. Upload the war file to S3
   aws s3 cp calculator-1.0.0.war s3://<your bucket name>/ForBeanstalk/
   
   # Note: It will create a folder 'ForBeanstalk' in your bucket and upload inside that

   Ex: aws s3 cp ~/calculator-app/target/calculator-1.0.0.war s3://trainings-101/ForBeanstalk/


Task 2: Create App in Elastic Beanstalk
=====================================
1. Goto AWS console for beanstalk

2. Click create application

  Application Name: calculator
  Platform: Tomcat
  Upload Your code: Select 'Public S3 URL' 
  -> Enter the URL https://trainings-2023.s3.amazonaws.com/ForBeanstalk/calculator-1.0.0.war

  Create App

3. It will take 6-7 minutes. Once the app is ready. Click on the webapp URL 
   and access the calculator


Task 3: New application version
===============================
1. Lets change the color of the calculator. In index.jsp, change '<body>' tag to  
   <body style="background-color:powderblue;">

2. Complete the change and build load. Upload war file to the same S3 bucket

3. In Elastic Beanstalk, goto application versions.
   click on deploy (latest version is already available in S3. so no need to upload)

4. Check the calculator page again and ensure that background has changed.


Task 4: CleanUp
===============
1. Delete the environment
2. Delete the application