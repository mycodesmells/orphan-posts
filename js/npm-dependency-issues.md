# 3 rules that will blow your JS mind

Some time ago, as a Java Script community we experienced an unprecedented situation when one of the developers unpublished his packages from npm registry. I don't see much point in debating whether he was right or wrong, but it prooved what I found out a long time ago - that Node's dependencies got out of hand. I want to blow your mind with three simple rules that are crazy in the JS world, but there is a sense behind their madness.

Note that these I havent't discovered those rules by any means, but they represent the ideas already present eg. in Go.

### 1. Copy vs dependency

If you ever saw an example unit test written in Go, you were probably scratching your head thinking _can't anyone come up with some library to handle those validations?_ I was one of them, and apparently wasn't the only one. I've talked with some people, saw many videos touching this exact problem. If you have no idea about Go, this is a snippet of a test:

    ...
    func TestShouldCalculateSum(t *testing.T) {
        result := calcuator.Sum(1, 2)

        expected := 3
        if result != expected {
            t.Errorf("Expected sum to equal %v but found: %v", expected, result)
        }
    }
    ...

Strange, isn't it? Anyone could probably create a simple library that would provide an API such as:

    func AssertEqual(expected int, result int, message string) error

Why is that the Go community does not use such utility? Because one of the mantras of Go is that _a little bit of copy is better than a bit of dependency_. You can never predict what can happen to that asserting library. Will it be broken in one of the future releases? Will it disappear suddenly? If you think about it, is it really worthy to depend on a third party solution to save three lines of code?

### 2. Keep dependencies in repo

Another rule from Go, where it's popular to actually download the dependencies and keep them within your project. Is it a waste of space? You have to ask yourself a question - what will happen to your project if those dependency break? Are you able to proceed then? What if Github or npm goes down for a day and you need to clone the project to make some urgent fixes before a release? How doomed are you?

You are probabaly concerned with a disk usage and the size of your repo. For example, using [Angular2-webpack-starter](https://github.com/AngularClass/angular2-webpack-starter) (which does include many useful things for a complete development such as testing framework for unit tests, e2e tests, live-reload server etc) results in over 150 megabytes of code. And this is just the beginning. Some time ago I got a _skeleton_ project setup for React JS that included Reflux, dome building tooling (with gulp), browserify and something more - over 300 megabytes.

So is it worth it? This is probably a per-project discussion. If you want to be independent in case of a worldwide IT disaster, you should value your project over a disk space. The space is not that expensive anyway.

### 3. Read their code

This is something that you should implement immediately. After that _npm gate_ unveiled, I came across multiple articles on how ridiculous npm dependencies have become. You might have already heard about [is-positive-integer](https://www.npmjs.com/package/is-positive-integer), [is-array](https://www.npmjs.com/package/is-array) or infamous [left-pad](https://www.npmjs.com/package/left-pad). Just reading those names make you think why on Earth would anyone use that? Can't they just do it themselves?

So my advice to you is to start reading the code of your dependencies. You can have multiple benefits from that. You can find some bugs in their behaviour (don't forget to send a PR to the author to make it better for everyone else), you can discover how it performs the thing you need it to do (expand your knowledge!) and finally, you might come to the conclusion that you actually don't need a `is-array` dependency, since its whole source code can be placed in a blog post without actually catching your attention (comments excluded):

    var isArray = Array.isArray;
    var str = Object.prototype.toString;
    module.exports = isArray || function (val) {
      return !! val && '[object Array]' == str.call(val);
    };

### Conclusion

You might not agree with the rules I mentioned here, because they are not natural to the JS community. You should be in doubt and a bit reluctant from applying them in your daily coding activities. But the thing I want to leave you with is: _Is staying in your comfort zone with current practices is really a place you want to be?_ Remember, that you are an expert, just like your doctor. You don't want to come to them and hear that they can't examine you, beause they lost a book they've used as a benchmark for your results, do you?
