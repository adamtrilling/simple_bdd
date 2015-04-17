# Simple BDD

Simple BDD offers basic Behaviour Driven Development language syntax. It enables tests to take steps to become more declaritive than imperiative by hiding the implementation and revealing test intent. It can be used in any test framework as it's just a way to keep the collaborative business language within a test by calling methods which directly relate to the step in the test.

## Installation

Add this line to your application's Gemfile:

``` ruby
gem 'simple_bdd'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install simple_bdd

### Integration with RSpec

To use SimpleBDD in your specs, simply add the following line to your spec helper:

``` ruby
require 'simple_bdd/rspec'
```

Or, if you want to have more control, you can do this instead:

``` ruby
require 'simple_bdd'

RSpec.configure do |config|
  config.include SimpleBdd
end
```

By default, SimpleBDD marks specs pending on missing step implementations.
You can change this behavior to raise an error instead in the spec helper:

``` ruby
RSpec.configure do |config|
  config.raise_error_on_missing_step_implementation = true
end
```

## Usage

### Standard Usage

The following will call commented method in scope of the current class or module:

``` ruby
[Gg]iven "Some state" # calls some_state
[Ww]hen "this happens" # calls this_happens
[Tt]hen "this change occurs" # calls this_change_occurs
[Bb]ut "this other thing still happens" # calls this_other_thing_still_happens
[Aa]nd "this other side effect happens" # calls this_other_side_effect_happens
```

Step definitions can be shared among multiple tests by repeating the same string, even with a different initial word.  In RSpec, describe, context, feature, and scenario each create an anonymous class which define the scope for your step definitions.

## Behavior Usage

Some additional methods allow you to group related behaviors in your tests:

``` ruby
Given "Admin is signed in"

behavior "admin can manage widgets" do
  When "Admin views all widgets"
  Then "Admin sees the first widget"
end

behavior "admin can manage factories" do
  When "Admin views all factories"
  Then "Admin sees the first factory"
end
```

Any of the following names can be substituted for `behavior` above:

* `and_by`
* `behaves_like`
* `behavior`
* `behaviour`
* `by`
* `it_also`

## Examples

### Unit testing

Simple BDD can be used to describe the overall behavior of a method or class.  Say you have a class that acts as a four-function calculator, with methods to add, subtract, multiply, divide, and retrieve the previous result.  You could write your test using RSpec and Simple BDD as follows:

``` ruby
describe 'Basic Math' do
  context 'I add two numbers' do
    Given 'I have a BasicMath'
    And 'I have two numbers'
    When 'I add the numbers'
    Then 'I get the sum'
  end

  context 'I subtract two numbers' do
    Given 'I have a BasicMath'
    And 'I have two numbers'
    When 'I subtract the numbers'
    Then 'I get the difference'
  end

  # shared by both contexts
  def i_have_a_basicmath
    @basic_math = BasicMath.new
  end

  # shared by both contexts
  def i_have_two_numbers
    @first_number = 7
    @second_number = 4
  end

  def i_add_the_numbers
    @basic_math.add(@first_number, @second_number)
  end

  def i_get_the_sum
    expect(@basic_math.result).to eq 11
  end

  def i_subtract_the_numbers
    @basic_math.subtract(@first_number, @second_number)
  end

  def i_get_the_difference
    expect(@basic_math.result).to eq 3
  end
end
```

### Feature testing

One of the most common uses for behavior-driven development is integration testing of an entire application.  When used with RSpec, Simple BDD integrates seamlessly with Capybara to provide a friendly syntax for feature specs:

``` ruby
feature 'Authentication' do
  scenario 'I log in' do
    Given 'I have an account'
    When 'I visit the login screen'
    And 'I log in'
    Then 'I am shown to be logged in'
  end

  let(:user) { User.create(username: 'simple_bdd', password: 'password') }

  def i_have_an_account
    user
  end

  def i_visit_the_login_screen
    visit '/sessions/new'
  end

  def i_log_in
    within("#session") do
      fill_in 'Username', :with => 'simple_bdd'
      fill_in 'Password', :with => 'password'
    end
    click_button 'Log in'
  end

  def i_am_shown_to_be_logged_in
    within('#header') do
      expect(page).to have_text('Hello, simple_bdd')
    end
  end
end
```

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
