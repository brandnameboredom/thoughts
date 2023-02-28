---
title: React-Router v4 Redirect with Redux
description: React-Router v4 Redirect with Redux
date: 2018-08-28
tags:
  - React
  - React-Router
  - Redux
  - Redirect
---

# Redirect

Today I was trying to redirect a user based upon a button click within a react child component.  It took me _way too long_ to get this to work.  So, I'm jotting this down here so I can find it again if I ever manage to strike the memories of today from my brain in favor of happier times.

First, the relevant dependencies from my package.json:

- react ^16.0.0
- react-router-dom ^4.2.2
- react-router-redux ^4.0.8
- react-redux v5.0.7

Second, my root component:

```
render(
            <Provider store={store}>
                <BrowserRouter basename={baseUrl}>
                    <App />
                </BrowserRouter>
            </Provider>,
            rootElement);
```

My App component loads a layout component which contains a Navigation component.  If you are like me, dear reader, at this point you are asking yourself "Why wouldn't you use a standard navigation solution?".  Well, good question.  Unfortunately the powers that be decided to out source the design before I was a part of the project and sold this layout and functionality to the customer. ...sigh... Anyway, my navigation component looked like:

```
class NavMenu extends Component {
    displayName = NavMenu.name

    render() {
            return (
                <div>
                    <ButtonToolbar bsClass={'btn-toolbar navigation'}>
                        <ToggleButtonGroup type={"radio"} name={"navOptions"} value={this.props.location.pathname}
                                           onChange={(e) => { this.props.history.push(e); }}>
                            <ToggleButton value={"/downloads"}>DOWNLOADS</ToggleButton>
                        </ToggleButtonGroup>
                    </ButtonToolbar>
                </div>
            );
    }
}

export default withRouter(connect(mapStateToProps)(NavMenu));
```

So, as you can see, on the change event of the toggle button group I was hoping to execute a ```this.props.history.push``` command in order to force the react-router to load the relevant route.  However, what I found was this was not the case.  The URL would update but the page would not refresh.  Some careful ```console.log``` commands revealed that I was properly pushing the path onto the history stack.  So, I asked one of my dear friends...Google.  I found [this post] which seemed to offer a few valid solutions.  I was particularly interested in the ```withRouter``` HOC solution.  But, as you can see in the code sample above I was already leveraging the ```withRouter``` command.  Hmm.  Strange.

After some head scratching and a bit more time scouring Google search results I found a random comment on a forum that suggested the root component may need the ```withRouter``` designation as well in order for the routing to be properly picked up along the chain.  So, I went back to my App component and wrapped it with a ```withRouter``` demarcation.  Sure enough, things started working as expected.  Sweet.  Now, let's hope I remember I put this here if I ever run into this issue again...

[redirect]: https://en.wikipedia.org/wiki/URL_redirection
[this post]: https://stackoverflow.com/a/42124328/70130
