# Assignment II: Let's move your stick man
*************************************************************
Last class,we have learn many knowledge about transformation.Now you need to complete the project II this week,I guess teacher has introduced the detail about this project,please create your stick man and let it move like a real man,I believe you can complete it perfectly.You can get the instruction from "No.5 Transformation" P63-82".
![image](https://github.com/ruange/stick-man/blob/master/stickman.gif)
*************************************************************
具体思路：stickman是由一段一段的cubes组合起来的，首先先在草稿纸上画出模型，确定每一个cubes的长宽高，使stickman看起来不那么别扭。然后在opengl中使用glvertex3f(x,y,z)函数确定四个点，glBegin(GL_QUADS)函数将使这四个点画出一个面，将长方体的6个面依次画出组成一个长方体，注意这些长方体都是在自己的坐标系画出的。接下来的问题是将这些长方体叠起来，叠起来需要有个层次，opengl中有个矩阵栈，存储的是坐标系变换的矩阵，glPushMatrix()将矩阵入栈，glPopMatrix()将矩阵出栈。栈底部的矩阵（坐标系）所做的变换都会影响栈上部的矩阵（坐标系）。

如图，我们以body中心为原点A，gltranslatef(0.0,1.2,0.0)即将坐标系向上移动1.2，到达B点，B点就是shoulder的中心，此时我们先不画shoulder，glPushMatrix()将矩阵入栈，glTranslatef(0.8, 0.0, 0.0)，glTranslatef(0.0, -0.4, 0.0)先向x轴正方向移动0.8，再向下平移0.4，到达 D1 点，D1 点即为上臂的中心，此时仍不画，glPushMatrix()将矩阵入栈，向下平移1.2，到达 D2 ，D2 为下臂的中心，此时可以调用DrawArmb()画出下臂，画完后我们需要回到上一个层次，调用glPopMatrix()，回到 D1 点，这里调用DramArma()函数画出上臂，glPopMatrix()回到B点，然后我们glPushMatrix()将矩阵入栈，向x轴负方向平移0.8，到达 C1 点，glPushMatrix()，向下平移0.4，到达 C2 ，画出下臂，pop之后画出上臂，pop又回到B点，依次类推，我们可以从B->E->F->E->B->A->G,从G我们可以画出左右两条leg，最后pop到A,画出body，此时整个stickman都已画出。![image](https://github.com/ruange/stick-man/blob/master/model.jpg)

虽然stickman已画出，但还不会动，要使stickman走动起来，需要手脚摆动起来，并且手上下臂摆动角度幅度不同，大小腿摆动的幅度也不一样。下臂的摆动要在上臂摆动的基础上，小腿的摆动要在大腿的基础上，这样才不会散架。实现这个很简单，只需要在 D1 点push之前进行一个glRotatef(angle1,x,y,z)操作即可，意思就是上臂绕着某一个轴旋转一个角度angle，那么push后下臂也会跟着上臂旋转一个angle角度，如果下臂有自己的旋转角度，只需在pop之前glRotate(angle2,x,y,z)即可。其他同理。为了使整个stickman看起来像走路，将最顶层的坐标系进行glTranslatef(x,y,z)操作。每次画完后都要改变angle等的值，再重画。angle值的变化是一个周期。
