# RXSWIFTPRACTICE
		c Declarative programming
		In computer science, declarative programming is a programming paradigm — a style of building the structure and elements of computer programs — that expresses the    		logic of a computation without describing its control flow. Declarative programming is an alternative of imperative programming
		For example, let’s say we have a property name text in the controller which is driving the label.
		In imperative world every time text property update we need to call the updateLabel method. Here we are manually controlling our program flow
		In declarative/reactive world we can somehow bind updateLabel method to this text property and whenever this property change our updateLabel method will react and       		executed
	
# layman terms:
	 -observable emit events which are subscribed or binded or observed . the subscriber gets immutable streams of values or sequence of data . 
	 -observale also gets data from some source 
	
# RxSwift
	Rx Swift is an async programming library that is based on observables. And observables are just an immutable sequence of data or events that you can react to. In the previous example property text which is String can be Observable sequence, initially this sequence is empty when it’s value change new value is added to this sequence without mutating previous value and since this property is bind to the observer which will react and update the label in our case . Since we are working with immutable sequence of data code that embraces some aspects of functional programming as well

# Observables:
	Observables is basically a wrapper around some data source and data source typically means a stream of values, since the main purpose of using RxSwift to do async programming easily, data coming from different sources over time will be put in this stream and will send to the observers. OR
	It allows other objects or consumers to subscribe for asynchronous events, or values, emitted by another object over time and produce a sequence of events that can “carry” an immutable snapshot of events/values

# Observable LifeCycle
	Observable can emit three types of events

	 .next → An event that “carries” the latest (or “next”) data value.

	.completed → This event terminates the event sequence with success. It won’t emit additional events after complete

	.error → The Observable terminates with an error and will not emit additional events.

	.dispose → To explicitly cancel a subscription, call dispose() on it. After you cancel the subscription, or dispose of it, the observable in the current example will stop emitting events. The .completed and .error will automatically call this method as shown below

# Creating Observable

 # just → Create an observable sequence containing just a single element.

	If you want just to transform a single value into the Observable the just() is probably more readable option to choose.

 	You don’t need to think about memory management when using just because it automatically call dispose 
	and .completed and free memory since it’s in the category of 	finite observable.
	
	just is a type method on Observable
	
![alt text](https://miro.medium.com/max/700/1*AMc8gDtxjQ69c9JRPUsGjQ.png)
 
# deferred → do not create the Observable until the observer subscribes, and create a fresh Observable for each subscription

	Sometimes you should read a value after the subscription.Take any iOS permission

	The above code may not work as you want. 
	You read the authorizationStatus while creating the Observable not when an observer subscribes to it.
	Imagine a user changes the permission in iOS settings before an observer subscribes for the permissionObservable. 
	The observer will receive the old value of the permission.

	func permissionObservable() -> Observable<PHAuthorizationStatus> 
	{ return Observable.deferred { return .just(PHPhotoLibrary.authorizationStatus()) }}

	Now observer will have a fresh permission status and your calculation will be calculated when the result is indeed needed.
	
	
# of → Convert any swift type to its corresponding Observable sequence.

	 You don’t need to think about memory management when using of because it automatically call dispose and free memory since it’s in the category of finite 		     
	 observable.
	 It behave like a constant after you initialize it with values you can’t add after that.
	 
![alt text](https://miro.medium.com/max/473/1*AwQaIrIqeQulzphns0Mkow.png)

# from → Creates an observable of individual elements from an array of typed elements.

	The from operator only takes an array.
	You also don’t need to think about memory management when using from operator because it automatically calls dispose and free memory since it’s in the category of finite observables.
	It also behaves like a constant after you initialize it with values you can’t add after that.
	
![alt text](https://miro.medium.com/max/700/1*tS_kib_dr0WIzH7NJrTVfQ.png)

# create → most flexible operator to create an Observable. You can make any library function reactive using create operator.
	You need to think about memory management when working with create operator
	It’s developer responsibilities to call completed, error and dispose method
	As shown below in Figure You just leaked memory. 
	Observables can store some variables inside theirs implementations or they can also store what you have passed to   
	them which means it is possible the Observables allocate some part of the memory for its internal needs.
	When you subscribe for an Observables the Disposable keeps 
	a reference to the Observables and the Observables keeps a strong reference to the Disposable (Rx creates some kind of a retain cycle here). 
	Now when user navigates back in navigation stack the Observables won’t be deallocated unless you want it to be deallocated.
	To break the retain cycle somebody needs to call dispose on the Observable.
	If Observable ends by itself (by sending completed or error) it will automatically         
	break the retain cycle. In any other scenario, 
	the responsibility to call the dispose function is in our hands.
	
	All the previous operators automatically free up the memory by calling dispose of after completed or error event but in this case,
	since we are creating custom Rx observable we need to call manually
	
![alt text](https://miro.medium.com/max/700/1*8DfX62eLNhOBdwh14Rz2Pw.png)

	As shown below after calling completed event which automatically call dispose and disposed the subscription,
	now our subscriber onDisposed method is triggered. 		
	Note: There is a dispose bag feature in RxSwift and we will cover this in future
	
![alt text](https://miro.medium.com/max/700/1*KDlOwfcUoAlN09wWJW-IBQ.png)

# Observer
	The observer is there to execute some code whenever it receives new data from Observables. 
	Observer bind with observable through subscription
	Example
	As shown in Figure 1 we have an observable representing a sequence of string which then observe by observer using the subscribe method and as a shown observer is   	reacting by printing immutable data provided by the observables
![alt text](https://miro.medium.com/max/700/1*qc8I5H-WCIcFn3U8etXK8w.png)
 
# Subject and Its Types
	Subject act as both an observable and an observer. 
	You will be using Subject in most cases in your application
	As shown in Figure 2 Rx Subject act both as observable and observer and one thing to note as I said earlier Observable representing async data and we added data 
	in Observable in an async fashion that was received by observer in an immutable way
![alt text](https://miro.medium.com/max/700/1*HKFSAOENaWETpBnQfL0gWg.png)

# Publish Subject
	Starts with empty and only emits new elements to its subscribers. 
	Let’s say if observable emitted n elements and new observer try to observe this observable it will receive n+1,n+2 and so on elements and will not receive 0 to n 	elements since it subscribed after they emitted values
	
	As shown in Figure 3 since Publish Subject is one of the type of Subject so it can acts both as Observable and Observer.
	Things to be noted Created an observable Using PublishSubject and the requirement of this type of subject is that it should starts with empty
	Binded Subject 1 (act as observer) with the above observable,
	Now Subject 1 will receive new element emitted by the observable after the subscription
	Added data to the observable sequence now it will send data to all the observers subscribed to it and in our case Subject 1 will get the new data
	
	Binded Subject 2 (act as observer) with the above observable, Subject 2 observer will not receive previous data as shown in the console
	Called Observable complete event which will mark the current subscribers as done and terminated and as shown in Figure 3 Subject 1 and Subject 2 completed and 		disposed event is called
	What if we added new subscriber after subject has emitted its completed event.
	In PublishSubject new subscriber will not receive previous next event but it will receive previous completed event and as shown below Subject 3 is added after subject has emitted its completed event and still get previous events
	
	We need to do memory management when working with PublishSubject by manually calling either completed or error or dispose to unsubscribe the observer and free up the memory and in our case since we call completed, this will eventually call dispose and free up the memory

![alt text](https://miro.medium.com/max/700/1*eO2NFibee6MLSeQBcGmenQ.png)

# Behaviour Subject
	Behavior subjects work similarly to publish subjects, except they will replay the latest .next event to new subscribers and it must start with some initial 		
	value. Let’s say if observable emitted n elements and new observer try to observe this observable it will receive n,n+1,n+2 and so on elements and will not 
	receive 0 to n-1 elements since it replay the latest .next event to new subscribers and in our case n is the latest
	
	As shown in Figure 4 since Behaviour Subject is one of the type of Subject so it can acts both as Observable and Observer. Things to be noted
	Created an observable Using BehaviorSubject and the requirement of this type of subject is that it should starts with some initial value
	Binded Subject 1 (act as observer) with the above observable, Now Subject 1 will replay the latest value which in this case Initial value we added
	Added data to the observable sequence now it will send data to all the observers subscribed to it and in our case Subject 1 will get the new data
	Binded Subject 2 (act as observer) with the above observable, Subject 2 observer will receive previous data as shown in the console
	Called Observable complete event which will mark the current subscribers as done and terminated and as shown in Figure 3 Subject 1 and Subject 2 completed and 	 
	disposed event is called
	
	What if we added new subscriber after subject has emitted its completed event. 
	If the observer(subject) subscribed after observable(subject) has emitted 
	completed / error event it will received that event only
	We need to do memory management when working with PublishSubject by manually calling either completed or error or dispose to unsubscribe the observer and free up 
	the memory and in our case since we call completed, this will eventually call dispose and free up the memory
![alt text](https://miro.medium.com/max/700/1*KqB8OUFWWJWRTkUdfO3Zgw.png)

	As shown in Figure 5 calling dispose on observable subject will simply telling observable don’t listen/bind/subscribe to new observer. In this case subject 3 (observer) tries to subscribe after observable subject dispose it will throw error and also disposed it and as shown below Subject 3 successfully disposed.
	What about Subject 1 and Subject 2 they leaked memory since both are not disposed yet because we call dispose on subject not on observer subject, previously we used completed which automatically call dispose on observer
	
	What about Subject 1 and Subject 2 they leaked memory since both are not disposed yet because we call dispose on subject not on observer subject, previously we used completed which automatically call dispose on observer
![alt text](https://miro.medium.com/max/700/1*yjRb7Xm_deAEBihPrwJ-3Q.png)

	 When you subscribe or bind to or drive from a RxSwift Observable, that subscription returns a Disposable. That disposable is basically a reference to that subscription and to that subscription’s entire Observable chain. Until that disposable is disposed, the subscription chain exists(unless the subscription receives a completed or error event) or if you are not calling completed/error you need to dispose manually and as shown in Figure 6 we are manually disposing our subscription by calling dispose on disposal 1 and disposable 2 and it will dispose Observer 1 and Observer 2 within viewDidLoad
	
 ![alt text](https://miro.medium.com/max/700/1*iikrpMgLFsHngfAWJuMZQQ.png)

# Dispose bag Recommended Approach

	If you forget to add a subscription to call dispose when no completed or error event is emitted when you’re done with the subscription, or in some other way cause the observable to terminate at some point, you will probably leak memory. The disposable returned by the Subscribe extension methods is returned solely to allow you to manually unsubscribe from the observable before the observable naturally ends. If the observable completes — with either OnCompleted or OnError — then the subscription is already disposed for you.

	DisposeBag is a bag (or collection) of disposables. when you subscribe to any observables you add disposables to this bag Because of adding the Disposable into the DisposeBag it means the DisposeBag have a strong reference to Disposable, since the bag is the controller property and when the view controller is deallocated, its variables (including the bag) are deallocated. When the disposeBag is deallocated, its deinit function calls dispose on all of the disposables it contains. Things to be noted

	If your controller have a memory leak , dispose bag deinit method will not call which means subscription will not disposed either
	Your Dispose Bag will clear its subscriptions only when the Dispose Bag’s owner is released. Therefore, you should be sure your Dispose Bag is tied to the life cycle of your subscriptions.

	Rule of Thumb
	Don’t create a dispose bag in one class, and share it with another class
	You should always add your subscriptions to a DisposeBag, even if you know your sequences will surely terminate (even compiler will not allow you so)
	Official suggestion is to always use .disposed(by: disposeBag) even though that’s not necessary for simple bindin
![alt text](https://miro.medium.com/max/700/1*HisVmxkV5RsjiXsN81L9vQ.png)
 
 # Replay Subject

	Replay subjects will temporarily cache, or buffer, the latest elements they emit, up to a specified size of your choosing. Let’s say if buffer = 2 and observable emitted n elements and new observer try to observe this observable it will receive n-1,n,n+1,n+2 and so on elements and will not receive 0 to n-2 elements since its replay the elements upto the buffer specified
	
	Keep in mind, when using a replay subject, that this buffer is held in memory. You can definitely shoot yourself in the foot, here, such as if you set a large buffer size for a replay subject of some type whose instances each take up a lot of memory, like images.
	
	As shown in Figure 1 since Replay Subject is one of the type of Subject so it can acts both as Observable and Observer. Things to be noted
	Created an observable Using ReplaySubject and the requirement of this type of subject is that we need to specify buffer size
	Binded Subject 1 (act as observer) with the above observable, Now Subject 1 will receive new element emitted by the observable after the subscription
	Added two elements to the observable sequence now it will send data to all the observers subscribed to it and in our case Subject 1 will get data
	Binded Subject 2 (act as observer) with the above observable, Subject 2 observer will receive previous data upto the buffer size as shown in Figure 1
	Called Observable complete event which will mark the current subscribers as done and terminated and as shown in Figure 3 Subject 1 and Subject 2 completed and disposed event is called
	What if we added new subscriber after subject has emitted its completed event. In ReplaySubject new subscriber will receive previous nextevent upto the buffer size and completed event as well
![alt text](https://miro.medium.com/max/700/1*aCOx3-Tta0WYNRk5WBcl2A.png)

# Publish Relay Subject
	A PublishRelay wraps a PublishSubject.What makes it different from PublishSubject is that it never terminated means you can’t call completed,error or dispose method on observable subject
	As shown in Figure 2 it is working as Publish Subject but their are few things to be considered
	There is no interface for subject to call dispose()
	There is no interface for subject to emit onNext ,onCompleted, onError event
	There is one new method accept which works same as onNext and in fact internally it call onNext to make onNext private
	Question is What is the purpose of PublishRelay if we have Publish Subject and people are raising question as well you can find answer in this link . In addition to this If you are working with existing project and you saw PublishRelay you are 100% sure it will not emit error or completed event so you don't need to cater this.

![alt text](https://miro.medium.com/max/700/1*KIdhD6HxtK5duMx2akwm4A.png)

# Behavior Relay Subject
	A BehaviorRelay wraps a Behavior Subject.What makes it different from Behavior Subject is that it never terminated means you can’t call completed,error or dispose method on observable subject and you can ask it for its current value without subscribing to receive updates which is very helpful

![alt text](https://miro.medium.com/max/700/1*Zb8-0pnj_fnzZrSIqhTOsA.png)

# Traits
	Traits are observables with a narrower set of behaviors than regular observables. Their purpose is to provide a way to more clearly convey your intent to readers of your code or consumers of your API
   # Single → 
	   A Single is a variation of Observable that, instead of emitting a series of elements, is always guaranteed to emit either a single element or an   	error. (Like Promise)
		One common use case for using Single is for performing HTTP Requests that could only return a response or an error, but a Single can be used to model any case where you only care for a single element, and not for an infinite stream of elements.
		Like just Observable it emits only one event to it’s subscriber
		.success(value) is actually a combination of the .next and .completed events.
![alt text](https://miro.medium.com/max/700/1*QCWj6aO5g02ZpC0JA8WzSw.png)

# Completable →
A Completable is a variation of Observable that can only complete or emit an error. It is guaranteed to not emit any elements. A useful use case 
for Completable would be to model any case where we only care for the fact an operation has completed, but don’t care about a element resulted by that completion.
Emits zero elements.
Emits a completion event, or an error.

# Maybe → 
	is the combination of Completable and Single. Maybe is useful when we want to write that an Observable might not have a value and will just complete. It can either emit a single element, complete without emitting an element, or emit an error.
	Emits either a completed event, a single element or an error.

# Operators
	Operators are method that apply to observable sequence and return another immutable observable sequence. Operator can be creating,filtering , transforming and combining operator we previously used some operators as well like just, of , from and so on . It is highly recommended to look http://reactivex.io/documentation/operators, The actual challenge will be how to use these operators in practice

# filter
	It takes a predicate closure, which it applies to every element emitted, allowing through only those elements which satisfy the predicate
![alt text](https://miro.medium.com/max/700/1*OHEmPZ_udoxFb27WIz1emA.png)

# skipWhile
	As shown in Figure 5 it is skipping element until first time predicate return false after that it starts passing through element and will not check condition and as shown it doesn’t skip last element

![alt text](https://miro.medium.com/max/700/1*Uf39FvsTVe74b_T_zIqyQg.png)

# distinctUntilChanged
	prevents duplicates that are right next to each other
![alt text](https://miro.medium.com/max/700/1*AfSKqE_yNOXTFa10A-mJtg.png)

 # toArray
	Convert an observable sequence of elements into an array of those elements once the complete event of observable is called .
	As shown in Figure nothing is printed on the console since onCompleted is not called yet
![alt text](https://miro.medium.com/max/700/1*InnbPhuMRUtULLEVVs2j5Q.png)
	After calling onComplete on the observable it will collect all the sequence element , convert it to array and send it to all the subscriber through next event method on the observable
![alt text](https://miro.medium.com/max/700/1*KqgOcdIii_XMkxoZ_dHOFA.png)

# map
	RxSwift map operator works just like Swift’s standard map, except it operates on observables. It except Observable as input (every time the Observable emits a new item) and return transformed Observable sequence

	As shown in Figure 6 we are getting User observable sequence by using map we transformed this observable sequence to UserDTO observable sequence. This is the operator you will be using very frequently
	Note: It will not depend on the .complete event on the observable, Every time element is added to the sequence it will apply map function and trigger .next event to all the observable
 ![alt text](https://miro.medium.com/max/700/1*GQZ6icAOUpMj_SVr9WMFqg.png)
 
 # flatMap
	The FlatMap operator transforms an Observable by applying a function that you specify to each item emitted by the source Observable, where that function returns an Observable that itself emits items. FlatMap then merges the emissions of these resulting Observables, emitting these merged results as its own sequence 😕
	This method is useful, for example, when you have an Observable that emits a series of items that themselves have Observable members or are in other ways transformable into Observables, so that you can create a new Observable that emits the complete collection of items emitted by the sub-Observables of these items.
	As shown in Figure 7 we have an Observable User that is emitting series of items user1,user2 and user3 that themselves have Observable members isConnected so flatmap will create three observables that is doing tracking of isConnected property and when this property change it send to all the observer with the value. Flatmap transformed inner observable by watching it’s observable property
	flatMap is similar to map, but it transforms element of observable to an observable of sequences.

 ![alt text](https://miro.medium.com/max/700/1*pq3M7xZ8sthWNLZM_JuZlg.png)


# share
	share operator when we need the chain of operators not to re-execute upon every subscription. Thus, everything before share is executed only once 😕
	As shown in Figure “Creating observable…” is called twice because create method gets executed as many times as we subscribe
	
![alt text](https://miro.medium.com/max/700/1*IXfVfZcVlljTCgf7ChM1QA.png)

	As shown in Figure 2 by using share operator now “Creating observable…” is called only one time and the observable is now share between all subscribers As the data emitted . by observable it will send to al observer as usual
![alt text](https://miro.medium.com/max/700/1*YlusohoCp24o6Jfq7A9XIg.png)

	One point to note if the observable is shared and there is no subscription attached or previous subscription is disposed then chain of operators re-execute for next subscription as shown in Figure 3
	
![alt text](https://miro.medium.com/max/700/1*JCaI77mQNBGtlI8eZze2yA.png)

	Following actions are performed
	Created a shared observable
	Subscribed first Observer and wait for it’s event, It emit next and completed since complete event dispose the subscription now there is no observer observing
	After that we tap on button and on its action we again want to subscribe the shared observable since no previous subscription is there it will again re execute the chain of operators

![alt text](https://miro.medium.com/max/700/1*s6LajD05v8fH70NuR-ZyIg.png)

	As shown in Figure 4 there are few things to be note
	Since second observer subscribed after first subscription get 1,2 and 3 next events so it will not get these. It will get the next event after thhe subscription
	There is only one “Creating observable…” printed on the console since first subscription didn’t dispose yet and (No complete event called)

 ![alt text](https://miro.medium.com/max/700/1*mCm00elb2-PbOPkklrMA6g.png)
 
	Question is How to get previous next event emitted before the second subscription and the answer to give parameter value to share operator. As shown in Figure 6 second observer subscribed after first subscription still it get 1,2 and 3 next events that were emitted before it subscribed
 ![alt text](https://miro.medium.com/max/700/1*fCQDG26N39IifAwItrngtA.png)
 
	 One more twist we added .forever as a scope while creating share observable which has the following means
	First subscription disposed by calling complete event and second observer subscribed after that with .forever it will not re-execute create method and with the replay 3 it will get previous 3 next event that were emitted before it subscribed
 ![alt text](https://miro.medium.com/max/700/1*YPZvPpRl3mYVf8SwWKrOmA.png)
 
 # Empty
Returns an empty observable sequence, using the specified scheduler to send out the single `Completed` message.
As shown in Figure 8 it create an Observable that emits no items and immediate terminates

  ![alt text](https://miro.medium.com/max/700/1*wxjApdp9cU8lN9zlfrWGmQ.png)
  
 Custom Operator (Creating Own Operator)
As shown in Figure 9 we created a BehaviorSubject of type Optional String , If we want only unwrap value there is no operator to unwrap any observable sequence

  ![alt text](https://miro.medium.com/max/700/1*XAwN3kSwOz3hgsElJfskxw.png)
  
We can solve this problem by doing two steps first filter nil observable value and then unwrap value of optional
  ![alt text](https://miro.medium.com/max/700/1*XYy8WTTXo4RT5tUKttvzXA.png)
  
As shown in Figure 8 we created Observable Type extension that return not nil values from the observable sequence. This method has following things
Restricted to Observable having Optional data Type (where E == T?)
Return Observable of unwrap Observable sequence. It should return Observable sequence because this operator we applying to observable.
“Operators are method that apply to observable sequence and return another immutable observable sequence.”definition we discussed in part 2

  ![alt text](https://miro.medium.com/max/700/1*9qw_fdHea3NPY9DkIyEAPg.png)
  
A shown in Figure 9 we replaced the filter and map operator with only one operator that can be part of Observable method which reduces code duplication, it is maintainable and testable as well
  ![alt text](https://miro.medium.com/max/700/1*bHg3fjnHmdvMOWfEL1SclQ.png)

