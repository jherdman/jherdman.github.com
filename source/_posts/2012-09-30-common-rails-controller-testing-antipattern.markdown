---
layout: post
title: "A Common Rails Controller Testing Antipattern"
date: 2012-09-30 23:18
comments: true
categories: testing rails controllers
---

Here's a run of the mill test for a Rails controller action:

```ruby
describe PostsController do
  describe 'GET #show' do
    let(:post) { mock_model(Post) }

    before do
      Post.stub(:find => post)
    end

    it 'loads the requested resource' do
      get :show, :id => post.id
      expect(assigns(:post)).to eq(post)
    end
  end
end
```

This isn't a horrible test, but there's a common antipattern where we are requesting the resource: we're explicitly using the record's database ID, line 10. All ActiveRecord objects have a URI friendly name via the [ActiveModel::Naming#to_param](https://github.com/rails/rails/blob/3-2-stable/activemodel/lib/active_model/conversion.rb#L49-53) method. Let's rewrite our test to use this method:

```ruby
describe PostsController do
  describe 'GET #show' do
    let(:post) { mock_model(Post) }

    before do
      Post.stub(:find => post)
    end

    it 'loads the requested resource' do
      get :show, :id => post.to_param
      expect(assigns(:post)).to eq(post)
    end
  end
end
```

This insulates us from changes to how a resource might be represented in the URI, and more accruately represents how Rails internally handles your resource with URI helpers. For more of the gory details, check out [ActionDispatch::Routing::PolymorphicRoutes](https://github.com/rails/rails/blob/3-2-stable/actionpack/lib/action_dispatch/routing/polymorphic_routes.rb).

### Edit

* Updated both examples to remove another common silly antipattern in my examples. A tip of the hat to [@camwest](https://twitter.com/camwest).
