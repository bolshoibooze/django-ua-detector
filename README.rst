

Server-side User Agent Detection
_______________________________

Back in 2013 i was trying to create a responsive news web app,but i hated the idea of having to redirect a sub-domain for the mobile site,e.g 'm.examplesite.com': seemed so 1995.

So i stumbled on a django package (i've long forgotten the original author,sadly) and mashed it up with some elegant views inspired by Nyaruka's nsms-plus.


What this app does:
____________________
1.Enables you to use a single view for serving pages to all user agents,from mobile to
  desktop.
  
2.Allows creation of a mobile-specific template and another one for tablets and desktop users

So in short you get to keep a unififed code base and make your web app responsive without using bloated javascript solutions :just write the proper media queries in your css


Quick Documentation
_____________________

1. Installation
   
   pip install django-ua-detector
   
   or simply clone this repo
   
   git clone https://github.com/bolshoibooze/django-ua-detector.git
   
1. Add 'ua_detector' to your INSTALLED_APPS ::
   INSTALLED_APPS = (
        ...
        'ua_detector',
    )
    
2. Include the ua_detector  URLconf in your project(root) 'urls.py' ::

   url(r'^ua_detector/',include('ua_detector.urls')),
   
   
3. Plug and play with responsive views --- a few use cases are in order:



View Examples
____________________

   In your views.py file::
   
   from ua_detector.views import *
   from ua_detector.generic_views import *
   from ua_detector.model_views import *
   
   class ExampleTemplateView(MobileTemplateView):
         template_name = 'example.html'  #desktop version
         mobile_template_name = 'm_example.html'  #the mobile version
         
   class SlickCreateView(ModelCreateView):
         model = Example #name of your model here 
         fields = ('foo','bar') #used in lieu of forms
         mobile_template_name='m_example_form.html'
         template_name='example_form.html'
         success_url = reverse_lazy('example_list')
    
         def form_valid(self, form):
             form.instance.user = self.request.user
             return super(SlickCreateView, self).form_valid(form)
             
    class ClassicCreateView(MobileFormView):
          mobile_template_name='m_example_form.html'
          template_name='example_form.html'
          form_class = MyCreateForm
          get_success_url = '/accounts/example_list/'
    
    
          def form_valid(self, form):
              form.save()
              return super(ClassicCreateView, self).form_valid(form)  
              
    class ExampleEditView(ModelUpdateView):
          model = Example
          fields = ('foo','bar',)
          mobile_template_name='m_example_form.html'
          template_name='example_form.html'
          #success_url = reverse_lazy('example_list')
    
          def form_valid(self, form):
              form.instance.user = self.request.user
              return super(ExampleEditView, self).form_valid(form)
 

    class ExampleDeleteView(ModelDeleteView):
          model = Example
          fields = ('foo','bar')
          mobile_template_name='m_example_form.html'
          template_name='example_form.html'
          success_url = reverse_lazy('example_list')
    
    def form_valid(self, form):
        form.instance.user = self.request.user
        #form.instance.bill.user = self.request.user
        return super(DeleteSubscription, self).form_valid(form)
    
   
                 
    class ExampleListView(ModelListView):
          model = Example
          mobile_template_name='m_example_list.html'
          template_name='services_list.html'
          
          
    class ListView(MobileListView):
          template_name = None
          mobile_template_name = None
          context_object_name = None
          model = None
          paginate_by = None

         def  get_context_data(self,**kwargs):
              context = super(ListView,self).get_context_data(**kwargs)
              context[''] = None
              return context
         
LICENSE
_______________
Free BSD
Use and abuse for both personal and commercial projects to your heart's desire

P/S:
_________
There are lots of other apps for making Django responsive (see: https://www.djangopackages.com/grids/g/mobile/), but they require middleware and a bunch of other settings like template tags inclusion,e.tc,e.t.c....e.t.c.

I think that's kinda over-kill for such a simple problem
