---
layout: post
title:      "Some React/Redux Project Tips From A Cat Dad"
date:       2018-07-12 15:00:38 -0400
permalink:  some_react_redux_project_tips_from_a_cat_dad
---

Ah, the final project for Flatiron’s curriculum. It’s truly the final countdown. React is an incredible tool, in tandem with Redux, that has arguably revolutionized the way people interact with web applications. Because of it's ability to update the page/screen at any given time through use of a virtual DOM, React allows for web applications that seem much more natural and fluid in responses to input.

However, it's also responsible for creating some colossal headaches (wouldn't be surprised if it was under the list of symptoms on WebMD at this point). In an effort to prevent some of those headaches, I'd like to share a couple things I discovered through working on my project, [8-bit Discourse](https://github.com/saurookadook/eight-bit-discourse).

## Rendering Components Dependent On Data From API
One realization I had after arriving at a working version of my app was that I noticed that my components were rendering correctly despite some of the fetch requests made in them actually failing. A bit of messing around and a further look at my code brought me to what was allowing for such a weird scenario: all of the information I needed from the API in order to load any one of my components was being loaded in the `App` component. This led me down a long rabbit hole of refactoring my components so that fetch requests were being made with more discretion, and after a lot of blood sweat and tears, I give you the following:

```
class PostPage extends Component {

  componentDidMount() {
    this.props.fetchPost(this.props.post)
  }

  render() {
    let hasData = (this.props.post.loaded && !this.props.post.loading)
    return (
      <div className="PostPage">
        { hasData ? (
          <PostDisplay props={this.props} />
        ) : (
          <LoadingPage />
        )}
      </div>
    )
  }
}

const mapStateToProps = (state, ownProps) => {
  if (!state.post.loaded) { 
    let postId = parseInt(ownProps.match.params.id)
    return {
	// long story behind this
      post: {
        loading: false,
        loaded: false,
        postId: postId
      }
    }
  } else {
    return {
      post: state.post
    }
  }
}

const mapDispatchToProps = (dispatch) => {
  return bindActionCreators({
    fetchPost: fetchPost
  }, dispatch);
};

export default connect(mapStateToProps, mapDispatchToProps)(PostPage);
```

Let’s walk through this a bit. You might be wondering, “What is with all the shenanigans in your `render` method?” Though it may seem excessive, the reason has to do with the order of operations, so to speak, of React. When my `PostPage` component is mounted, `mapStateToProps` and `mapDispatchToProps` will be run first (thanks to Redux), then it will try to invoke `render()`, due to the vacancy of a `constructor` or any other lifecycle method with a higher priority than `render()`. At this first pass of attempting to render the component, it will break if any calls for information return `undefined`. In my case, it would happen in my `PostDisplay` component:

```
const PostDisplay = ({ props }) => {
  let postId = parseInt(props.match.params.id);
  return (
    <div className="PostDislay">
      <Post post={props.post} />
      <div className="comments">
        <CommentsList comments={props.post.comments} />
        <CommentForm postId={postId} />
      </div>
    </div>
  )
}

export default PostDisplay;
```

At this point, `props.post.comments` will be undefined and, therefore, break. In order to continue using `componentDidMount` to make fetch requests to my API, I created a `LoadingPage` presentational component, which is rendered until `hasData` is true. Upon successfully retrieving data, `mapStateToProps` is called again due to a change in state (this actually happens a couple times ), and eventually `state.post` will have the attributes `loading: false, loaded: true`, as well as all of the necessary information in order to render `PostDisplay` properly. Recognizing the call to `mapStateToProps`, Redux will then trigger a re-render of the page, this time with `PostDisplay` instead, since `hasData` will now return true.

### Some thoughts:
Though this approach works, I can’t help but feel that having a `loading` and `loaded` attribute for each part of store reliant on information from an API seems not only excessive, but very prone to error. However, my reasoning for using both is that while just having `loading` would be make it easier (set default value to `true`, which changes to `false` in the appropriate reducer, and change `hasData` in conditional to `!this.props.post.loading` in order to render `PostDisplay`), it creates confusion when conceptualizing the flow of the application. The first time the component renders, is the data from your API actually loading/fetching? No, that doesn't happen until the component containing a fetch request is mounted (or attempting to mount). Another reasoning for this implementation is for potentially more flexibility in the flow of your reducer and the possibility of additional control flow within your components.

## (Temporary) User Authentication
Another big ol’ elephant in the room for this project is dealing with user authentication (assuming that's something normal for the application being built). From the opinions of various colleagues, it's been commonly mentioned that user authentication in React is quite difficult. While that is something I'm interested in learning to configure, I wanted my project to handle adding users in a way that was meaningful but similar to the way in which It might be handled in a real application. Again, my solution isn’t ideal (nor should/would it ever be part of a functioning application in the real world), but for the sake of being able to mimic the flow of how a site with users might function, I came up with the following for the `create` actions in my controllers:

```
class CommentsController < ApplicationController
  skip_before_action :verify_authenticity_token, only: [:create]

  …

  def create
    
    @user = User.find_by(username: params[:comment][:user])
    
    if !@user
      @user = User.create(username: params[:comment][:user], password: SecureRandom.hex(10))
    end

    @post = Post.find(params[:post_id])

    @comment = @post.comments.build(user_id: @user.id, post_id: params[:comment][:postId], content: params[:comment][:content])
    if @comment.valid?
      @post.save
      render json: @post, include: ['author', 'comments', 'comments.user']
    else
      render json: { message: "There was an issue submitting your comment, please try again."}
    end
  end

   …
 
End
```

Before getting to anything, I’ll address what I’m sure is an immediate concern for most of you: no `strong_params` function? Eventually, yes, when I’m able to more dynamically control information related to `Users`. For the intents and purposes of this project, however, the long way works just fine, even if it is uglier than roadkill. Another immediate concern may be the second line, `skip_before_action :verify_authenticity_token, only: [:create]`, though this is simply a quick fix to ensure that Rails won’t throw an `InvalidAuthenticityToken` error upon submission of my form. Again, not the cleanest (or most secure) solution, but it at least allows the project to mimic both proper handling of information and a realistic conversation between the frontend and backend of your application.

## Questions/Comments?
Overall, this project was a huge challenge, but has definitely helped to deepen my understanding of just about every aspect of React and Redux. Following my review, there are several things I plan to improve and to learn how to incorporate additional features (i.e. user authentication, image uploads, etc.), so please feel free to reach out if you have any ideas for improving upon the examples above or anything else in my project. I’d similarly be happy to answer any questions you might have! You can find me on [GitHub](https://github.com/saurookadook) or in Slack: @Andrew Maskiell. 
