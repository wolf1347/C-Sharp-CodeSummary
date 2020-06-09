# Overview:

The project that I had the opportunity to work on was a pre-existing code base for a web application being created for a Theater company using ASP .Net MVC and Entity Framework. Since it was a pre existing code base that was near completion I was able to work on both back end and front end stories to enhance the existing features. It was a great experience that really allowed me to focus on skills pertaining to learning how an existing application functions and working within its confines.
	
# Custom Scrollbar:

My first story was creating a custom scrollbar for the website that fit the overall theme and style of the website. I chose to use both Webkit and CSS Scrollbars in order to maximize the amount of browsers the scrollbar would be functional with. In order to make the feature more user friendly a hover effect was used to highlight the bar and buttons as you hovered over them and a background: linear gradient in order to recreate the scrollbar arrow buttons.

    
    /* using webkit to make scrollbar styling compatible with Chrome, Edge(most recent version), and Safari*/
    ::-webkit-scrollbar { /* This effects the width of the scrollbar. If the width is adjusted the buttons (arrows) must be adjusted as well*/
        width: 14px; /* Horizontal Scrollbar*/
        height: 14px; /* Vertical Scrollbar */
    }
    
    /* Track */
    ::-webkit-scrollbar-track {
        background: var(--light-color); 
    }
    
    /* Thumb */
    ::-webkit-scrollbar-thumb {
        background: var(--main-bg-color); 
    }
    ::-webkit-scrollbar-thumb:hover {
        background: var(--main-secondary-color);
    }
    
    /* Buttons */
    ::-webkit-scrollbar-button {
        height: 18px; /* Horizontal Scrollbar*/
        width: 18px; /* Vertical Scrollbar */
    }
    
    /* The below code is to recreate the arrow shape of the buttons*/
    ::-webkit-scrollbar-button:vertical:start:decrement {
        background: linear-gradient(120deg, var(--light-color) 40%, rgba(0, 0, 0, 0) 41%), linear-gradient(240deg, 
        var(--light-color) 40%, rgba(0, 0, 0, 0) 41%), linear-gradient(0deg, var(--light-color) 30%, rgba(0, 0, 0, 0) 31%);
        background-color: var(--main-bg-color);
    }
    
    ::-webkit-scrollbar-button:vertical:start:decrement:hover {
        background-color: var(--main-secondary-color);
    }
    
    ::-webkit-scrollbar-button:vertical:end:increment {
        background: linear-gradient(300deg, var(--light-color) 40%, rgba(0, 0, 0, 0) 41%), linear-gradient(60deg, 
        var(--light-color) 40%, rgba(0, 0, 0, 0) 41%), linear-gradient(180deg, var(--light-color) 30%, rgba(0, 0, 0, 0) 31%);
        background-color: var(--main-bg-color);
    }
    
    ::-webkit-scrollbar-button:vertical:end:increment:hover {
        background-color: var(--main-secondary-color);
    }
    
    ::-webkit-scrollbar-button:horizontal:end:increment {
        background: linear-gradient(210deg, var(--light-color) 40%, rgba(0, 0, 0, 0) 41%), linear-gradient(330deg, 
        var(--light-color) 40%, rgba(0, 0, 0, 0) 41%), linear-gradient(90deg, var(--light-color) 30%, rgba(0, 0, 0, 0) 31%);
        background-color: var(--main-bg-color);
    }
    
    ::-webkit-scrollbar-button:horizontal:end:increment:hover {
        background-color: var(--main-secondary-color);
    }
    
    ::-webkit-scrollbar-button:horizontal:start:decrement {
        background: linear-gradient(30deg, var(--light-color) 40%, rgba(0, 0, 0, 0) 41%), linear-gradient(150deg, 
        var(--light-color) 40%, rgba(0, 0, 0, 0) 41%), linear-gradient(270deg, var(--light-color) 30%, rgba(0, 0, 0, 0) 31%);
        background-color: var(--main-bg-color);
    }
    
    ::-webkit-scrollbar-button:horizontal:start:decrement:hover {
        background-color: var(--main-secondary-color);
    }
    
    /* this section is applicable only to the styling of the scrollbar on Firefox. Used in conjunction with webkit for better compatibility*/
    
    * {
        scrollbar-width: auto; /* This shows a message that it is not a known CSS property but is supported in FireFox and will show as expected when launched with that browser. */
        scrollbar-color: var(--main-bg-color) var(--light-color); /* first color effects the thumb and the second color effects the track*/
    }
 
# Seed Parts:

The next story I worked on was a back end story to have the database add and update data upon the application running. The Parts are added to a new list and then it iterates through the list and the result of the AddOrUpdate will be determined by what is returned in to the tempPart variable. If no match between production title, character, and type are found a new record is created in the database. If a match is found it will instead update.

    //Seeding database with dummy Parts
    private void SeedParts()
    {
        var parts = new List<Part> 
        {
            new Part{
                Production = context.Productions.Where(p => p.Title == "Hamilton").FirstOrDefault(), 
                Character="Alexander Hamilton", 
                Type=Enum.PositionEnum.Actor, 
                Person= context.CastMembers.Where(c => c.Name == "London Bauman").FirstOrDefault(), 
                Details="The ten-dollar Founding Father without a father, Alexander Hamilton starts out as a " 
                "penniless immigrant but rises up in the ranks and becomes an aide to George Washington himself. After " 
                "the American Revolution, he becomes one of the most prominent politicians of the young United States, " 
                "creating USA's financial system, writing in defense of the Constitution and founding the Federalist 
                Party."},

            new Part{
                Production= context.Productions.Where(p => p.Title == "Hamilton").FirstOrDefault(),
                Character="Devon Roberts",
                Type=Enum.PositionEnum.Director,
                Person= context.CastMembers.Where(c => c.Name == "Devon Roberts").FirstOrDefault(),
                Details="The director manages the creative aspects of the production. They direct the making of a film by " 
                "visualizing the script while guiding the actors and technical crew to capture the vision for the screen. " 
                "They control the film's dramatic and artistic aspects"},
        };

        parts.ForEach(x => 
        {
            var tempPart = context.Parts.Where(p => p.Production.Title == x.Production.Title && p.Character == x.Character && p.Type == x.Type).FirstOrDefault(); 
            if (tempPart != null) 
            {
                x.PartID = tempPart.PartID; 
            }
            context.Parts.AddOrUpdate(p => p.PartID, x); 
        }); 
        context.SaveChanges();
    }  

# Search Null Error:

I was also given a story to resolve an error resulting from the Search function on the website. It allowed the user to search through the archives of productions and return the results. The error we were running in to was a SearchNullError where if the search did not return any results the application would stop and display the error within Visual Studio which was due to an unhandled exception in the code. In order to resolve this I wrapped each search in a try and catch statement so it would still return the search results while also catching the exception.
 
     switch (searchByCategory)
    {
        case "SearchAll": //This case searches across all three tables.
            ViewBag.Message = string.Format("Results for \"{0}\" in Archive", searchKey);
            var resultsCast = new List<CastMember>();
            foreach (CastMember castMember in db.CastMembers)
            {
                try 
                {
                    Match matchName = rx.Match(castMember.Name);
                    Match matchYearJoined = rx.Match(castMember.YearJoined.ToString());
                    Match matchBio = rx.Match(castMember.Bio);
                    if (matchName.Success || matchYearJoined.Success || matchBio.Success)
                    {
                        resultsCast.Add(castMember);
                    }
                }
                catch (Exception e)
                {
                }
            }
                    
# Parts Card in Production Details:
My last story that I worked on was a front end story utilizing bootstrap for styling and Razor Syntax to utilize C# code within the HTML file. For this story I needed to include some responsive design with the size of the screen. When the screen was MD or larger the Parts Card would be displayed at the left of the screen with the other cards however if the screen was SM or smaller it would be hidden and instead the code at the bottom of the screen would be displayed.

For the parts card it needed to iterate through all of the parts within the database and display the information within the card. If there was no data to display it would not display the section within the card. The way that I was able to accomplish this was by accesing the data specified for each type of part (Actor, Director, Etc.) in a variable, if the variable returned any information and was not null the group item would display, and then for each item within the variable it would display a column with that information returned.
 
     <!-- Parts Card Section visible when screen size is bigger-->
              <div class="card bg-info mr-4 d-none d-md-block" id="cardss"> <!-- when screen size is med or bigger this will display. It is otherwise hidden-->
                <div class="card-header" id="head">
                  <b>Parts</b>
                </div>
                <ul class="list-group list-group-flush text-black-50">

                  <!--Iterate through Parts for a specific Type of role and if there is data it will display the below item in the Parts card. If no data is returned it will not display-->

                  @{var actor = Model.Parts.Where(t => t.Type == TheatreCMS.Enum.PositionEnum.Actor); 
                    if (actor != null && actor.Any()) 
                    {
                      <li class="list-group-item">
                        Actors
                        <dl>
                          @foreach (var item in actor)
                          {
                            if (item != null)
                            {
                              <dd class="col">
                                <a href="/Part/Details/@item.PartID">@Html.Raw(@item.Character)</a>
                              </dd>
                            }
                          }
                        </dl>
                      </li>
                    }
                  }
 
 
    <!-- Parts Card Section visible when screen size small-->

            <div class="card bg-info mr-4 d-md-none" id="cardss">
              <!-- when screen size is md or bigger this will not display. So when screen size is sm/mobile view it will be visible but otherwise hidden -->
              <div class="card-header" id="head">
                <b>Parts</b>
              </div>
              <ul class="list-group list-group-flush text-black-50">

                <!--Iterate through Parts for a specific Type of role and if there is data it will display the below item in the Parts card. If no data is returned it will not display-->

                @{var actorsm = Model.Parts.Where(t => t.Type == TheatreCMS.Enum.PositionEnum.Actor);
                  if (actorsm != null && actorsm.Any())
                  {
                    <li class="list-group-item">
                      Actors
                      <dl>
                        @foreach (var item in actorsm)
                        {
                          if (item != null)
                          {
                            <dd class="col">
                              <a href="/Part/Details/@item.PartID">@Html.Raw(@item.Character)</a>
                            </dd>
                          }
                        }
                      </dl>
                    </li>
                  }
                }
# What I've Learned:

It was an invaluable experience being able to work with a nearly completed pre existing codebase and keeping the team coding practices and styles in mind when working on my stories. Working with ASP .Net MVC, Entity Framework, Razor Syntax, and Bootstrap all gave me valuable skills for the future in not only with utilizing these tools but how to better educate myself on and research for future tools as well. 

I also was able to learn a lot working with a team in an agile setting. We worked in two week sprints, doing 20-30 minute daily standups, and a weekly retrospective. It was very helpful being able to hear what everyone else was working on, interesting things they had found, roadblocks they were coming accross, their best practices, and discussing ways we could all improve in the next week. 

Overall I had a great time with this project and gained a lot of skills working with both back end and front end stories and being part of a larger team. 



