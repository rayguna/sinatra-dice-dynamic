# sinatra-dice-dynamic

[Notes for this project are here: _Dynamic path segments_](https://learn.firstdraft.com/lessons/111)

<b>Goal:</b> To implement dynamic path segments.

Approach: 
- Link the dynamic page in app.rb using the command block: get("/dynamic/:number_of_dice/6") do.
- Within the command block, call flexible.erb in which the random numbers for roll die is generated.

Activities:

- Run the initial code with the command bin/dev. I see a page similar to shown below:
<pre>

Home    2d6   2d10    1d20    5d4   

<b> Dice Roll </b>
  •  Roll two 6-sided dice
  •  Roll two 10-sided dice
  •  Roll one 20-sided die
  •  Roll five 4-sided dice

</pre>

- Created flexible.erb and link the file to app.rb with the code below. Note that a do loop is used to simplify the task. Update related files (views/layout.erb and views/homepage.erb).

```
# /app.rb

get("/dynamic/50/6") do
  @rolls = []

  50.times do
    die = rand(1..6)

    @rolls.push(die)
  end

  erb(:flexible)
end
```

- Make the path dynamic by modifying the above code to the code below. The name ":number_of_dice" is a dynamic path. At this point, however, we haven't set the number of dice to change according to the set number_of_dice.

```
# /app.rb

get("/dynamic/:number_of_dice/6") do
  @rolls = []

  50.times do
    die = rand(1..6)

    @rolls.push(die)
  end

  erb(:flexible)
end
```

- By simply adding an extra line "<%= params %>" as shown below allows you to extract the dynamic path. "<%= params %>" is a hash data type which contains a key-value pair, e.g., {"number_of_dice"=>"50"}, where "number_of_dice" is the key and "50" is the value.

```
<!-- /views/flexible.erb -->

<%= params %>

<h1>50d6</h1>

<ul>
  <% @rolls.each do |a_roll| %>
    <li>
      <%= a_roll %>
    </li>
  <% end %>
</ul>
```

- Within app.rb, if you change the route from get("/dynamic/:number_of_dice/6") do
 to get("/dynamic/:alice/6"), the key-value pair becomes {"alice"=>"50"} 

- Change the code within app.rb as shown below to extract the value of "number_of_dice" from the dynamic hash into a variable called @num_dice. Pass the variable into the do loop to match the number of random numbers to be generated.

```
# /app.rb

get("/dynamic/:number_of_dice/6") do
  @num_dice = params.fetch("number_of_dice").to_i

  @rolls = []

  @num_dice.times do
    die = rand(1..6)

    @rolls.push(die)
  end

  erb(:flexible)
end
```

- Pass the variable "@num_dice" from app.rb to flexible.erb as to be displayed dynamically follows:

```
<!-- /views/flexible.erb -->

<h1><%= @num_dice %>d6</h1>

<ul>
  <% @rolls.each do |a_roll| %>
    <li>
      <%= a_roll %>
    </li>
  <% end %>
</ul>
```

- Also make the dice type dynamic using the same approach. Modify the get statement in app.rb as shown below. Note that "number_of_dice" and "how_many_sides" are preceeded by colon. Also, pass the @sides parameter into the random number generator loop.

```
# /app.rb

#get("/dynamic/:number_of_dice/6") do
get("/dynamic/:number_of_dice/:how_many_sides") do
  @num_dice = params.fetch("number_of_dice").to_i

  @sides = params.fetch("how_many_sides").to_i

  @rolls = []

  @num_dice.times do
    die = rand(1..@sides)

    @rolls.push(die)
  end

  erb(:flexible)
end
```

- By embedding the tag <%=params %> in flexible.erb (see below), you can view the key-value pair being displayed. It should look as follows when you set the two values to 5 and 4, respectively.

```
<!-- /views/flexible.erb -->

<%= params %>

<h1><%= @num_dice %>d6</h1>

<ul>
  <% @rolls.each do |a_roll| %>
    <li>
      <%= a_roll %>
    </li>
  <% end %>
</ul>
```

- In the final version, simply embed the hash values into the heading as follows:

```
<!-- /views/flexible.erb -->

<h1><%= @num_dice %>d<%= @sides %></h1>

<ul>
  <% @rolls.each do |a_roll| %>
    <li>
      <%= a_roll %>
    </li>
  <% end %>
</ul>
```

- Now that we have put all things in place, we finally change the route name from dynamic to make it uniform with the rest to dice in app.rb, layout.erb, and homepage.erb as follows:

```
# /app.rb

#get("/dynamic/:number_of_dice/6") do
get("/dice/:number_of_dice/:how_many_sides") do
  @num_dice = params.fetch("number_of_dice").to_i

  @sides = params.fetch("how_many_sides").to_i

  @rolls = []

  @num_dice.times do
    die = rand(1..@sides)

    @rolls.push(die)
  end

  erb(:flexible)
end
```

layout.erb

```...
    <div class="navbar">
      <div><a href="/">Home</a></div>
      <div><a href="/dice/2/6">2d6</a></div>
      <div><a href="/dice/2/10">2d10</a></div>
      <div><a href="/dice/1/20">1d20</a></div>
      <div><a href="/dice/5/4">5d4</a></div>
      <div><a href="/dice/50/6">50d6</a></div>
    </div>
  ...
```

- homepage.erb

```
<ul>
  <li><a href="/dice/2/6">Roll two 6-sided dice</a></li>
  <li><a href="/dice/2/10">Roll two 10-sided dice</a></li>
  <li><a href="/dice/1/20">Roll one 20-sided die</a></li>
  <li><a href="/dice/5/4">Roll five 4-sided dice</a></li>
  <li><a href="/dice/50/6">Roll fifty 6-sided dice</a></li>
</ul>
```

Target: http://dice-roll.matchthetarget.com/
