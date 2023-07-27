<h1 align="center">BLoC Fundamentals (Cubit)</h1>
<uL></uL>
<li><b><a href=#t1>Why we need BLoC?!</a></b></li>
<li><b><a href=#t2>What are BLoC and Cubit?</a></b></li>
<li><b><a href=#t3>Important terms.</a></b></li>
<li><b><a href=#t4>Cubit in details with an application.</a></b></li>
<li><b><a href=#t5>Q&A.</a></b></li>
<li><b><a href=#t6>Debug and observe your BLoC easily.</a></b></li>

<h2 id=t1>Why we need BLoC?!</h2>
<p>To answer this question, let's construct a simple small application with 'flutter create bloc_fundamentals' command.</p>
In lib, make sure that these 3 files exist :
<ul>
  <li>main.dart</li>
  <li>screen1.dart</li>
  <li>screen2.dart</li>
</ul>
<p id=ma><b>main.dart :</b></p>

    import "package:flutter/material.dart";
    import "package:bloc_fundamentals/screen1.dart";
    void main()=>runApp(const MyApp());
    class MyApp extends StatelessWidget{
      const MyApp({super.key});
      @override
      MaterialApp build(final BuildContext context)
      => MaterialApp(home:Screen1());
    }

<p id=s1><b>screen1.dart :</b></p>
A screen that have two buttons :
<ul>
  <li>One for changing its own color.</li>
  <li>Other for navigating to Screen2.</li>
</ul>

    import "package:flutter/material.dart";
    import "package:bloc_fundamentals/screen2.dart";
    class Screen1 extends StatefulWidget{
      const Screen1({super.key});
      @override
      State<Screen1> createState()=>_Screen1State();
    }
    class _Screen1State extends State<Screen1>{
      Color _buttonColor = Colors.blue;
      Color screen1Color = Colors.white;
      @override
      Widget build(final BuildContext context)
      => Scaffold(
        backgroundColor:screen1Color,
        body:Center(
          child: Column(
            mainAxisAlignment:MainAxisAlignment.center,
            children:[
              MaterialButton(
                child:const Text("Change Color"),
                color:_buttonColor,
                onPressed:(){
    
                  // ---------------------------------------------
                  // Changing the color of button and rebuilding :
                  // ---------------------------------------------
    
                  setState(()=>_buttonColor=Colors.green);
                }
              ),
              SizedBox(height:10),
              MaterialButton(
                child:Text("Navigate to Screen2"),
                color:Colors.teal,
                onPressed:(){
                  Navigator.push(
                    context,
                    MaterialPageRoute(
                      builder:(final BuildContext context)=>Screen2() 
                    )
                  );
                }
              )
            ]
          ),
        )
      );
    }
<p id=s2><b>screen2.dart :</b></p>
<p>A screen that have one button to change the background color of Screen1.</p>

    import "package:flutter/material.dart";
    class Screen2 extends StatefulWidget {
      Screen2({super.key});
      @override
      State<Screen2> createState() => _Screen2State();
    }
    class _Screen2State extends State<Screen2> {
      @override
      Widget build(final BuildContext context)
      => Scaffold(
        appBar: AppBar(),
        body:Center(
          child:MaterialButton(
            color:Colors.blue,
            child:Text("Change background color of screen1"),
            onPressed:(){
              
              // --------------------------------------------------------------
              // How can we change the color of screen1 from this screen ? 🤔
              // --------------------------------------------------------------
              
            }
          )
        )
      );
    }
Think about a solution for this problem.<br/>
You may think of making the variable <var>screen1Color</var> global in Screen1 and changing it <var>onPressed</var> function of Screen2 with calling <var>setState</var> function.
<strong>This won't work.</strong>
Because <var>setState</var> function rebuilds only its own stateful widget.<br/>
<p align=center><img src=assets/try1.gif height=300></p>
<h3>So, we need BLoC ( The state management ) to solve this problem for any number of screens.</h3>

<h2 id=t2>What are BLoC and Cubit?</h2>
Both are used to control the states of our applications.<br/>
Cubit is a subset of BLoC; so it reduces complexity.
Constructing a Cubit is easier compared to constructing a BLoC, that’s why it is also easier to understand it compared to BLoC.
So, we gonna deal with Cubit in this repository and you can later migrate to BLoC if you needed.

<h2 id=t3>Important terms.</h2>
<table align="center">
  <thead>
    <tr>
      <th>Term</th>
      <th>Definition</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>BlocProvider</td>
      <td>A Flutter widget which provides the bloc (or cubit). Everytime you use BlocProvider, you construct a new bloc which isn't related to the other blocs.</td>
    </tr>
    <tr>
      <td>BlocBuilder</td>
      <td>A Flutter widget which rebuilds all its children ( and descendants ) in response to each new states.</td>
    </tr>
    <tr>
      <td>BlocListener</td>
      <td>A Flutter widget which keeps listening to state changes to perform actions based on these changes.</td>
    </tr>
    <tr>
      <td>BlocConsumer</td>
      <td>= BlocBuilder + BlocListener</td>
    </tr>
    <tr>
      <td>BlocObserver</td>
      <td>An abstract class for monitoring BLoC instances behavior.</td>
    </tr>
  </tbody>
</table>
<p>Don't worry if you didn't understand the previous terms. They will be clear in the incoming sections insha'allah.</p>
<h2 id=t4>Cubit in details with an application.</h2>
<p>We gonna use cubit to solve the problem of our application. But for better understanding, we gonna use it first to change the color of button in screen1.</p>
<ol>
  <li>Firstly, <a href=https://pub.dev/packages/flutter_bloc/install target=_blank>install flutter_bloc package</a>.</li>
  <li>Go to <a href=#s1>screen1.dart</a> file.</li>
  <li>Wrap Scaffold into BlocConsumer&lt;CubitClass, CubitStates&gt;.<br/> You can use BLoC extension in your IDE to do this easily.<br/>
    Two arguments are required for BlocConsumer (listener,builder).
    <p>After wrapping, the code must be like this :</p>

    BlocConsumer<CubitClass, CubitStates>(
      listener: (BuildContext context,CubitStates state) {
        /* listener function is usually used to execute some actions based on the value of state.
           We will show an example later. */
      },
      builder: (BuildContext context,CubitStates state) {
        return Scaffold(  /* This function must return the widget to be rebuild with its
                             descendants, and we want to rebuild Scaffold for the change
                             of backgroundColor, so we returned it. */
          // The rest of code ...
        );
      }
    )
  </li>
  <li>
    After wrapping into BlocCosumer, you will get error. This is because you can't use BlocConsumer (,BlocBuilder or BlocListener) without
    a BlocProvider. Remember (From <a href=#t3>terms section</a>) that it's the one which provides the cubit to be able to use BlocConsumer,Builder and Listener. So, you must wrap BlocConsumer into BlocProvider&lt;CubitClass,CubitStates&gt;.<br/>
    Two arguments are required for BlocProvider (create,child).<p>After wrapping, the code must be like this :</p>
      
      BlocProvider<CubitClass,CubitStates>(
        create: (BuildContext context) => CubitClass(),  /* Cascade operator can be used here
                                                            to execute initializations.
                                                            Ex : CubitClass()..openDB() */
        child: BlocConsumer(
          listener:(BuildContext context,CubitStates state){
          
          },
          builder:(BuildContext context,CubitStates state){
          return Scaffold(
              // The rest of code ...
            )
          }
        ),
      );
  After doing the prev, you will also get an error again 😅; because CubitClass,CubitStates don't exist. So, we gonna construct them.
  </li>
  <li>Make a new folder called cubit and make two files in it :</li>
  <ul>
    <li>cubit.dart<br/>
  </li><li>states.dart</li>
  </ul>
  <li><p>In cubit.dart :</p>
    
    import "package:flutter_bloc/flutter_bloc.dart";
    import "package:flutter/material.dart";
    
    // constructing the CubitClass which must extend Cubit
    class CubitClass extends Cubit<CubitStates>{  /* Cubit takes a class called CubitStates.
                                                     We gonna construct it in the next step. */
      Color buttonColor = Colors.blue;  /* This is the variable which we will use
                                           to change the button color. */
    
      CubitClass():super(CubitInitialState());  /* This line is required. We passed an object
                                                   of a class to the parent (Cubit class).
                                                   This class (CubitInitialState) must be a
                                                   subclass of CubitStates class, which we
                                                   used above while constructing CubitClass. */
    
      void changeColor(Color newColor){  /* A method constructed manually to do our task
                                            (changing the color of button). */
        buttonColor=newColor;
        emit(CubitChangeColorState());  /* emit function is equivalent to setState((){})
                                           in stateful widgets, but it takes a subclass of
                                           CubitStates (or CubitStates class itself) as a
                                           parameter. So, CubitChangeColorState must be a
                                           class that extends CubitStates class. */
      }
    }
  </li>
  <li>The next step, which is the easiest one, is to just construct the classes needed for cubit.dart file.<br/>
      <p>In states.dart :</p>

    // We will just build some empty classes.
    abstract class CubitStates{ /* We made it abstract; because we won't construct an
                                   object from it. It's optional to make it abstract. */
    
    }
    class CubitInitialState extends CubitStates{
      
    }
    class CubitChangeColorState extends CubitStates{
    
    }
  Don't forget to import the needed files in each one.
  </li>
  <li>
    Finally, we gonna edit screen1.dart code to apply the changes :
    <p>screen1.dart after editing :</p>

      import "package:flutter/material.dart";
      import "package:bloc_fundamentals/screen2.dart";
      import "package:flutter_bloc/flutter_bloc.dart";
      import "package:bloc_fundamentals/cubit/cubit.dart";
      import "package:bloc_fundamentals/cubit/states.dart";
      
      class Screen1 extends StatefulWidget{
        const Screen1({super.key});
        @override
        State<Screen1> createState()=>_Screen1State();
      }
      class _Screen1State extends State<Screen1>{
        Color _buttonColor = Colors.blue;
        Color screen1Color = Colors.white;
        @override
        Widget build(final BuildContext context)
        => BlocProvider(
          create: (context) => CubitClass(),
          child: BlocConsumer<CubitClass, CubitStates>(
            listener: (BuildContext context,CubitStates state) {
              if(state is CubitInitialState)
                print("We are now in the initial state");
              else if(state is CubitChangeColorState);
                print("We are now in change color state");
              /* Everytime emit function is called, listener is called and state argument takes
                 the class passed to emit function. */
      
              /* Note that listener isn't called by openning the app, so the first condition
                 will never be executed. */
            },
            builder: (BuildContext context,CubitStates state) {
              CubitClass cubit = BlocProvider.of<CubitClass>(context);
              /* BlocProvider.of(context) returns an instance of CubitClass related to the
                 given context. This line of code must be memorized because it's primary to
                 use the cubit functions and fields. */
              return Scaffold(
              backgroundColor:screen1Color,
              body:Center(
                child: Column(
                  mainAxisAlignment:MainAxisAlignment.center,
                  children:[
                    MaterialButton(
                      child:const Text("Change Color"),
                      color:cubit.buttonColor,  // getting the buttonColor from cubit.
                      onPressed:(){
          
                        // ---------------------------------------------
                        // Changing the color of button and rebuilding :
                        // ---------------------------------------------
          
                        // setState(()=>_buttonColor=Colors.green);
                        
                        cubit.changeColor(Colors.green); // calling a function from cubit.
                      }
                    ),
                    SizedBox(height:10),
                    MaterialButton(
                      child:Text("Navigate to Screen2"),
                      color:Colors.teal,
                      onPressed:(){
                        Navigator.push(
                          context,
                          MaterialPageRoute(
                            builder:(final BuildContext context)=>Screen2() 
                          )
                        );
                      }
                    )
                  ]
                ),
              )
            );
            },
          ),
        );
      }
  Now, try clicking "Change color" button, you will get the same result as before and the color will be changed successfully.
  <p align=center><img src=assets/try2.gif height=300></p>
  </li>
</ol>
<h4>After the previous task, you must have understood most of our topic. I gonna ask you to solve the main problem of our application, which is changing the color of Screen1 background from Screen2. Take the following hint and try solving it before coming back to see the solution.</h4>
<table align=center>
  <thead>
    <th><i>Hint</i> : Use passing data between screens.</th>
  </thead>
</table>
<h3>Problem solution :</h3>
<ol>
  <li>Go to cubit.dart file to add the needed logic :
    
    import "package:bloc_fundamentals/cubit/states.dart";
    import "package:flutter_bloc/flutter_bloc.dart";
    import "package:flutter/material.dart";
    
    // constructing the CubitClass which must extend Cubit
    class CubitClass extends Cubit<CubitStates>{  /* Cubit takes a class called CubitStates.
                                                     we gonna construct it in the next step. */
      Color buttonColor = Colors.blue;  /* This is the variable which we will use
                                             to change the button color. */
    
    
      Color screen1Color = Colors.white; // <-----
    
      CubitClass():super(CubitInitialState());  /* This line is required. We passed an object
                                                   of a class to the parent (Cubit class).
                                                   This class (CubitInitialState) must be a
                                                   subclass of CubitStates class, which we
                                                   used above while constructing CubitClass. */
    
      void changeColor(Color newColor){  /* A method constructed manually to do our task
                                            (changing the color of button). */
        buttonColor=newColor;
        emit(CubitChangeColorState());  /* emit function is equivalent to setState((){})
                                           in stateful widgets, but it takes a subclass of
                                           CubitStates (or CubitStates class itself) as a
                                           parameter. So, CubitChangeColorState must be a
                                           class that extends CubitStates class. */
      }
    
      void changeScreen1Color(Color newColor){ // Constructing the required function.
        screen1Color=newColor;
        emit(CubitChangeScreen1ColorState());
        /* Note that emit function and fields can be used from cubit instance [ cubi.emit( ) ],
           but the best practice is to keep the logic in cubit class. */
      }
    }
  </li>
  <li>
    Add CubitChangeScreen1ColorState to states.dart :
    
    class CubitChangeScreen1ColorState extends CubitStates{
    
    }
  </li>
  <li>Ask yourself : Where is the widget to be rebuilt?<br/>
    Answer : It's Scaffold in Screen1, but it will be rebuilt from Screen2.<br/>
    So, <b>We have two options </b>:
    <ol>
      <li>Passing the context of Screen1 to Screen2 and making the cubit instance in Screen2.</li>
      <li>Passing the cubit itself to Screen2.</li>
    </ol>
    The second option is simpler.
    So, we gonna use it.
    <p>screen2.dart after editing :</p>

    import "package:flutter/material.dart";
    import "package:bloc_fundamentals/cubit/cubit.dart";
    
    class Screen2 extends StatefulWidget {
      CubitClass s1Cubit; // New variable to receive the cubit
      Screen2({super.key,required this.s1Cubit}); // Required cubit.
      @override
      State<Screen2> createState() => _Screen2State();
    }
    class _Screen2State extends State<Screen2> {
      @override
      Widget build(final BuildContext context)
      => Scaffold(
        appBar: AppBar(),
        body:Center(
          child:MaterialButton(
            color:Colors.blue,
            child:Text("Change background color of screen1"),
            onPressed:(){
              
              // --------------------------------------------------------------
              // How can we change the color of screen1 from this screen ? 🤔
              // --------------------------------------------------------------
    
              widget.s1Cubit.changeScreen1Color(Colors.red);  // calling the function
              /* widget keyword is used to access fields and functions of the statefulWidget
                 in state class. */
            }
          )
        )
      );
    }
  </li>
  <li>The last step is to give <b>cubit.screen1Color (instead of screen1Color) to Scaffold backgroundColor</b> and pass the cubit from screen1 :

    Navigator.push(
      context,
      MaterialPageRoute(
        builder:(final BuildContext context)=>Screen2(s1Cubit:cubit) 
      )
    );
  </li>
</ol>
The problem must be solved successfully.
<p align=center><img src=assets/try3.gif height=300></p>
<h3>You may be still have some questions. I added the following section to help answering it.</p>
<h2 id=t5>Q&A.</h2>
<ul>
  <li>Q: If I have a code like the following, and I want to rebuild Widget1 and Widget3 only. Where should I put BlocProvider and BlocConsumers?
    
      Column(
        children:[Widget1(), Widget2(), Widget3()]
      )
  A/ Wrap Column by BlocProvider, and wrap each of Widget1 and Widget2 by BlocConsumer.
  </li>
  <li>Q: If I want to rebuild a popup widget (like AlertDialog, SnackBar, BottomSheet, ...etc). Should I give them a BlocProvider?<br/>
    A/ No, you don't have to give them a BlocProvider (like different screen). Just, give them a BlocConsumer (or a BlocBuilder),<br/>
    &nbsp; &nbsp; &nbsp;but they must be called from a widget under a BlocProvider.
  </li>
  <li>Q: Is it a must to use statefulWidget with BLoC (or Cubit)?<br/>
    A/ No, you don't have to use statefulWidget if you don't need it (like our example : we could convert it to stateless widgets).
  </li>
  <li>Q: Where should I put BlocProvider if I want to rebuild multiple screens?<br/>
    A/ You have multiple ways :
    <ul>
      <li>You can put one BlocProvider at the top of the widget tree (above the MaterialApp) and then you can use BLoC anywhere in the application.</li>
      <li>You can use a BlocProvider in a screen, pass the BLoC instance to other screens, and finally pass it to BlocConsumers (Builders or Listenres) through bloc argument. You can also pass the context of BlocProvider and make the bloc instance [BlocProvider.<CubitClass>of(passedContext)] while passing to bloc argument.</li>
      <li>You can use a BlocProvider in a screen and use BlocProvider.value in other screens and then pass the BLoC instance to value argument of BlocProvider.value in each of the other screens.</li>
    </ul>
  </li>
  <li>Q: What are BlocBuilder and BlocListener ?<br/>
    <p>A/ Simply,</p>

    BlocBuilder<CubitClass, CubitStates>(
      builder: (context, state) { // Required parameter.
        // Return widget here based on CubitClass's state.
      }
      // Optional parameters :
      bloc:     // Takes the bloc (or cubit) instance of CubitClass.
      buildWhen:  // Condition of triggering emit to rebuild. (Boolean value)
    )
  <h3 align="center">+</h3>

    BlocListener<CubitClass, CubitStates>(
      listener: (context, state) { // Required parameter.
        // do stuff here based on CubitClass's state.
      }
      // Optional parameters :
      bloc:    // Takes the bloc (or cubit) instance of CubitClass.
      listenWhen:  // condition of triggering emit to call listener. (Boolean value)
      child:  // Takes a child widget.
    )
  <h3 align="center">=</h3>

    BlocConsumer<CubitClass,CubitStates>(
      // Required parameters :
      listener: (context, state) {
        // do stuff here based on CubitClass's state.
      },
      builder: (context, state) {
        // return widget here based on CubitClass's state.
      },
      // Optional parameters :
      bloc: 
      buildWhen:
      listenWhen:
    )
  </li>
</ul>
<h2 id=t6>Debug and observe your BLoC easily.</h2>
<p>To do that, edit <a href=#ma>main.dart</a> file to be like the following code :</p>

    import "package:flutter/material.dart";
    import "package:flutter_bloc/flutter_bloc.dart";
    import "package:bloc_fundamentals/screen1.dart";
    void main(){
      Bloc.observer=MyBlocObserver();  // Calling the observer.
      runApp(const MyApp());
    }
    class MyApp extends StatelessWidget{
      const MyApp({super.key});
      @override
      MaterialApp build(final BuildContext context)
      => MaterialApp(home:Screen1());
    }

    // The following class can be got from bloc documentation :
    class MyBlocObserver extends BlocObserver {
      @override
      void onCreate(BlocBase bloc) {
        super.onCreate(bloc);
        print('onCreate -- ${bloc.runtimeType}');
      }
    
      @override
      void onChange(BlocBase bloc, Change change) {
        super.onChange(bloc, change);
        print('onChange -- ${bloc.runtimeType}, $change');
      }
    
      @override
      void onError(BlocBase bloc, Object error, StackTrace stackTrace) {
        print('onError -- ${bloc.runtimeType}, $error');
        super.onError(bloc, error, stackTrace);
      }
    
      @override
      void onClose(BlocBase bloc) {
        super.onClose(bloc);
        print('onClose -- ${bloc.runtimeType}');
      }
    }
Just copy MyBlocObserver class from <a href=https://pub.dev/packages/bloc target=_blank>bloc documentation</a>.<br/>
You can now observe bloc (and cubit) instances easily.<br/>
<p>Example of an output :</p>
<code>flutter: onChange -- CubitClass, Change { currentState: Instance of 'CubitChangeColorState', nextState: Instance of 'CubitChangeScreen1ColorState' }</code><br/>
The previous output line indicates that the state changes from CubitChangeColorState to CubitChangeScreen1ColorState.<br/>
You can observe the construction, change, errors, close of bloc using BlocObserver.<br/>
<h3>To learn more about bloc and cubit, just read the docs : <a href=https://pub.dev/packages/flutter_bloc target=_blank>flutter_bloc</a>, <a href=https://pub.dev/packages/bloc target=_blank>bloc</a>.</h3>
<h2 align=center>سبحانك اللهم وبحمدك، أشهد أن لا إله إلا أنت، أستغفرك وأتوب إليك.</h2>
<h3 align=center>Glory is to You, O Allah, and praise; I bear witness that there is none worthy of worship but You. I seek Your forgiveness and turn to You in repentance.</h3>
