RxJavaFX: JavaFX bindings for RxJava
NOTE: UNSUPPORTED, PLEASE FOLLOW FORKS FOR NEWER DEVELOPMENTS AND DEPLOYMENTS
https://github.com/torakiki/RxJavaFX

Read the free eBook Learning RxJava with JavaFX to get started.

RxJavaFX is a lightweight library to convert JavaFX events into RxJava Observables/Flowables and vice versa. It also has a Scheduler to safely move emissions to the JavaFX Event Dispatch Thread.

NOTE: To use with Kotlin, check out RxKotlinFX to leverage this library with extension functions and additional operators.

Master Build Status


Documentation
Learning RxJava with JavaFX - Free eBook that covers RxJava from a JavaFX perspective.

Learning RxJava - Packt book covering RxJava 2.0 in depth, with a few RxJavaFX examples.



1.x Binaries
Binaries and dependency information for Maven, Ivy, Gradle and others can be found at http://search.maven.org.

Example for Maven:

<dependency>
    <groupId>io.reactivex</groupId>
    <artifactId>rxjavafx</artifactId>
    <version>1.x.y</version>
</dependency>
Gradle:

dependencies {
	compile 'io.reactivex:rxjavafx:1.x.y'
}
2.x Binaries
RxJavaFX 2.x versions uses a different group ID io.reactivex.rxjava2 to prevent clashing with 1.x dependencies. Binaries and dependency information for Maven, Ivy, Gradle and others can be found at http://search.maven.org.

Example for Maven:

<dependency>
    <groupId>io.reactivex.rxjava2</groupId>
    <artifactId>rxjavafx</artifactId>
    <version>2.x.y</version>
</dependency>
Gradle:

dependencies {
	compile 'io.reactivex.rxjava2:rxjavafx:2.x.y'
}
Features
RxJavaFX has a comprehensive set of features to interop RxJava with JavaFX:

Factories to turn Node, ObservableValue, ObservableList, and other component events into an RxJava Observable
Factories to turn an RxJava Observable or Flowable into a JavaFX Binding.
A scheduler for the JavaFX dispatch thread
Node Events
You can get event emissions by calling JavaFxObservable.eventsOf() and pass the JavaFX Node and the EventType you are interested in. This will return an RxJava Observable.

Button incrementBttn = new Button("Increment");

Observable<ActionEvent> bttnEvents =
        JavaFxObservable.eventsOf(incrementBttn, ActionEvent.ACTION);
Action Events
Action events are common and do not only apply to Node types. They also emit from MenuItem and ContextMenu instances, as well as a few other types.

Therefore, a few overloaded factories are provided to emit ActionEvent items from these controls

Button ActionEvents
Button incrementBttn = new Button("Increment");

Observable<ActionEvent> bttnEvents =
        JavaFxObservable.actionEventsOf(incrementBttn);
MenuItem ActionEvents
MenuItem menuItem = new MenuItem("Select me");

Observable<ActionEvent> menuItemEvents = 
        JavaFxObservable.actionEventsOf(menuItem);
Other Event Factories
There are also factories provided to convert events from a Dialog, Window or Scene into an Observable. If you would like to see factories for other components and event types, please let us know or put in a PR.

Dialogs and Alerts
Alert alert = new Alert(AlertType.CONFIRMATION);
alert.setTitle("Confirmation");
alert.setHeaderText("Please confirm your action");
alert.setContentText("Are you ok with this?");

JavaFxObservable.fromDialog(alert)
    .filter(response -> response.equals(ButtonType.OK))
    .subscribe(System.out::println,Throwable::printStackTrace);
Emitting Scene Events
Observable<MouseEvent> sceneMouseMovements =
     JavaFxObservable.eventsOf(scene, MouseEvent.MOUSE_MOVED);

sceneMouseMovements.subscribe(v -> System.out.println(v.getSceneX() + "," + v.getSceneY()));
Emitting Window Hiding Events
 Observable<WindowEvent> windowHidingEvents =
    JavaFxObservable.eventsOf(primaryStage,WindowEvent.WINDOW_HIDING);

windowHidingEvents.subscribe(v -> System.out.println("Hiding!"));
ObservableValue
Not to be confused with the RxJava Observable, the JavaFX ObservableValue can be converted into an RxJava Observable that emits the initial value and all value changes.

TextField textInput = new TextField();

Observable<String> textInputs =
        JavaFxObservable.valuesOf(textInput.textProperty());
Note that many Nodes in JavaFX will have an initial value, which sometimes can be null, and you might consider using RxJava's skip() operator to ignore this initial value.

ObservableValue Changes
For every change to an ObservableValue, you can emit the old value and new value as a pair. The two values will be wrapped up in a Change class and you can access them via getOldVal() and getNewVal(). Just call the JavaFxObservable.changesOf() factory.

SpinnerValueFactory<Integer> svf = new SpinnerValueFactory.IntegerSpinnerValueFactory(0, 100);
Spinner spinner = new Spinner<>();
spinner.setValueFactory(svf);
spinner.setEditable(true);

Label spinnerChangesLabel = new Label();
Subscription subscription = JavaFxObservable.changesOf(spinner.valueProperty())
        .map(change -> "OLD: " + change.getOldVal() + " NEW: " + change.getNewVal())
        .subscribe(spinnerChangesLabel::setText);
