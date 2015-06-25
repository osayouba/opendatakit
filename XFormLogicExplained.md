<font color='red'>This page is not necessarily current.</font> Please see our form design help pages at http://opendatakit.org/help/form-design/ for the most recent documentation.

The forms can contain both Skip Logic and data constraints.
The way it's done in the XML is a section called BIND.

For every piece of data you are collection (say, question 1) there is a node in the Instance.

```
<instance>
   <data>
      <question1/>
   </data>
</instance>
```

for ever Node, there can be a BINDing (though it's not necessary).

`<bind nodeset="/data/question1"   />`

All by itself, that doesn't do anything. But, if you want the question to be required, this is where you add that control.

`<bind nodeset="/data/question1" required="true()"  />`

Now, let's say that you want to add a data constraint. Question 1 is this, "What is your Age?"
You can't interview children, so the answer must be older than 18. You don't want someone accidentally putting in the year of their birth, or some other crazy number, so you constrain the number to be less than 100. Oh, and it has to be a number, not a word.

`<bind nodeset="/data/question1" required="true()" constraint=". &gt; 17 and . &lt; 100"   type="int" />`

If someone puts in a crazy number for age, it won't accept it. You might want to add an error message, so you can do that too:


`<bind nodeset="/data/question1" required="true()" constraint=". &gt; 17 and . &lt; 100"  jr:constraintMsg="Subject must be an adult, between 18 and 99 years old."  />`

There are lots of kinds of data constraints, you can use regular expressions in there, like if the answer must be a 3 digit number, you can constrain like this:

`<bind nodeset="/data/question1" required="true()"   type="int"  constraint="regex(., '^\d\d\d$')" jr:constraintMsg="The number must be 3 digits" />`

A lot of people think these kinds of expressions are awesome, I just think they are regular. Anyway, useful stuff.

Notice that there is a datatype constraint in there as well: `type="int"`
This will make sure only numbers (integers) can be entered and will pop up the numerical keyboard for the user.

Other types of types are:
```
date
time
geopoint
string
```

there are some others, they control how the interface presents to the user. If the `type="geopoint"` then the user will see a control to get and record the GPS position.

Datatypes are useful in combination with Preloaders, for example, you can record the start time of your survey (or the end time, or some other time) like this:

`<bind nodeset="/data/start" type="time" jr:preload="timestamp" jr:preloadParams="start"/>`

That records the time that the survey starts and records it into a node called "start".

You asked about Skip Logic as well, and you do that in the BIND also by making use of `Relevant` property.
You basically add this to a question saying, THIS question should be presented to the user IF the answer to a previous question is THAT.

Q1 asks "Do you have a dog or a cat?"
Q2 asks "What is your dog's name?"
Q3 asks "What is your cat's name?"

So, Q2 is only RELEVANT if Q1's answer is "dog". Get it?
Q3 is only RELEVANT if Q1's answer is "cat".

So, in the bind, it gets written like this:

```
<bind nodeset="/data/q1"  />
<bind nodeset="/data/q2"  relevant="/data/q1= 'dog'"  />
<bind nodeset="/data/q3"  relevant="/data/q1= 'cat'"  />
```

ODK will only present the cat question to cat owners, the dog question to dog owners.

There are multiple ways to use the RELEVANT property.

Like the Dog/Cat question above, Relevant IF some other question equals some value.

`relevant="/data/q16 = '88'"`

If you want to ask ten questions about dogs and ten questions about cats, you just give the appropriate relevant="/data/q1= 'dog'" to all the dog questions and the same for the cat. So, you can skip whole sections that way.

You can combine more than one Relevant value by putting "and" between them.

`relevant="/data/q5= '1'  and /data/q6 = '88'"`

You can also use negatives, that is, Question is Relevant IF another question's value does NOT equal something.

`relevant="/data/q5 != '0'"`

When working with multiple-select questions `<select>`, where users can select more than one answer, you can't use node='value' because there may be more than one value in there. So, you can use  selected(node, 'value')

`relevant="selected(/data/q11, '88')"`

Yes, it can be combined with other Relevants for other nodes

`relevant="/data/q9 = '1' and selected(/data/q11, '88')"`

You can even say THIS question is Relevant only IF the multiple choice question does NOT have a certain value selected.

`relevant="not(selected(/data/q11, '0'))"`


OK, that's what I have to say about that. I write a lot of skip logic and data constraints, so I have figured out some tricks. Also some caveats, like the ODK Form Validator (though it is awesome, all praise to the mighty Validator) does not get all mistakes in the BIND.

If you are writing some BIND statements, and you refer to a non-existent node (like you mix up the letter 'O' and the number '0' or something like that, you have a node called /data/O01, but you spell it /data/OO1) the validator will not catch it, but your form will crash like a lead balloon.

You can watch it crash with the adb logcat, and you will see an error message "Node does not exist".

Good luck with your stuff, m0j0 OUT!