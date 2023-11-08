---
layout: post
read_time: true
show_date: true
title: "Rails: Tab menülerde paginate işlemi"
date: 2016-09-24 08:58:22 +0200
img: banners/codes.png
tags: [coding, rails]
category:
author: Ecmel Albayrak
---

Tab menülerde paginate işlemini yapılandırırken karşılaştığım sorunlar:

İlk önce tablar arasında geçişlerde bir sıkıntı yaşadım. Bunu [will_paginate](https://github.com/mislav/will_paginate)'in `param_name` parametresiyle şöyle hallettim.

```ruby
@teachers = Teacher.paginate(page: params[:teachers_page], per_page: 10)
@students = Student.paginate(page: params[:students_page], per_page: 10)

<%= will_paginate @teachers, param_name: "teachers_page" %>
<%= will_paginate @students, param_name: "students_page" %>
```

Diğer bir sorun ise ilk tab dışındaki diğer tablarda paginate işlemiyle başka sayfalara geçiş yaptığım zaman (?pages=2) sistem sayfayı ilk tab'ın konumuna atıyordu.

Yani ``yapmak istediğim ama aslında olan`` tam da şu şekildeydi:

<iframe src="//giphy.com/embed/GQTHzCNx0UgdW" width="480" height="270" frameBorder="0" class="giphy-embed" allowFullScreen style="display: block;margin: 0 auto;margin-bottom:20px"></iframe>

Bu sorunu da HTML5'in localStorage'ını kullanarak şu şekilde hallettim:

<!--more-->

```ruby
# GET /users/1
def show
  @user_activities_for_course_weeks = PublicActivity::Activity.includes(:trackable, :owner).where(owner_id: User.find(params[:id]), trackable_type: "CourseWeek").paginate(page: params[:course_weeks], per_page: 10)
  @user_activities_for_teachers = PublicActivity::Activity.includes(:trackable, :owner).where(owner_id: User.find(params[:id]), trackable_type: "Teacher").paginate(page: params[:teachers], per_page: 10)
end
```

```erb
<ul id="activityTab" class="nav nav-tabs">
  <li class="active"><%= link_to "Course Weeks", "#tab_week", id: "profile-tab", role: "tab", "data-toggle" => "tab" %></li>
  <li><%= link_to "Teachers", "#tab_teacher", role: "tab", id: "profile-tab", "data-toggle" => "tab" %></li>
</ul>
<div class="tab-content">
  <%= render "week_activities" %>
  <%= render "teacher_activities" %>
</div>
```

```erb
<div role="tabpanel" class="tab-pane fade active in" id="tab_week">
  <ul class="messages">
    <% @user_activities_for_course_weeks.each do |activity| %>
      <li>
        ...
      </li>
    <% end %>
  </ul>
  <%= will_paginate @user_activities_for_course_weeks, class: "apple_pagination", param_name: "course_weeks" %>
</div>
```

```erb
<div role="tabpanel" class="tab-pane fade" id="tab_teacher">
  <ul class="messages">
    <% @user_activities_for_teachers.each do |activity| %>
      <li style="list-style-type: none;">
        ...
      </li>
    <% end %>
  </ul>
  <%= will_paginate @user_activities_for_teachers, class: "apple_pagination", param_name: "teachers" %>
</div>
```

```js
$('a[data-toggle="tab"]').on('show.bs.tab', function(e) {
  localStorage.setItem('activeTab', $(e.target).attr('href'));
});

var activeTab = localStorage.getItem('activeTab');

if (activeTab) {
  $('#activityTab a[href="' + activeTab + '"]').tab('show');
}
```

Farklı çözüm önerileriyle aydınlandığım vakit burayı güncelleyeceğim.
