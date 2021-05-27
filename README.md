import time
import pandas as pd
import numpy as np

CITY_DATA = { 'chicago': 'chicago.csv',
              'new york city': 'new_york_city.csv',
              'washington': 'washington.csv' }

def get_filters():
    """
    Asks user to specify a city, month, and day to analyze.

    Returns:
        (str) city - name of the city to analyze
        (str) month - name of the month to filter by, or "all" to apply no month filter
        (str) day - name of the day of week to filter by, or "all" to apply no day filter
    """
    print('Hello! Let\'s explore some US bikeshare data!')
    # TO DO: get user input for city (chicago, new york city, washington). HINT: Use a while loop to handle invalid inputs
    city = input('which city do you want to see its data: Chicago , New York City, or Washington? ')
    while city not in (CITY_DATA.keys()):
        print('you choose an invalid city!')
        city = input('which city do you want to see its data: Chicago , New York City , Washington? ').lower()
    # TO DO: get user input filter (month, day, both, all)
    filter = input('Do you like to filter data by month , day , both or none? ').lower()
    while filter not in ['month' , 'day' , 'both' , 'none']:
        print('Invalid Filter!')
        filter = input('Do you like to filter data by month , day , both or none? ').lower()
    # TO DO: get user input for month (all, january, february, ... , june)
    months = ['january', 'february', 'march', 'april', 'may','june']
    if filter == 'month' or filter == 'both':
        month = input('which month do you like to see its data: january, february, march, april, may, june? ').lower()
        while month not in months:
            print('you choose an invaled month!')
            months = input('which month do you like to see its data: january, february, march, april, may, june? ').lower()
    else:
         month = 'all'
    # TO DO: get user input for day of week (all, monday, tuesday, ... sunday)
    days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday','Friday', 'Saturday']
    if filter == 'day' or filter == 'both':
        day = input('which day you want to see its data: Sunday, Monday, Tuesday, Wednesday, Thursday, Friday, Saturday? ').title()
        while day not in days:
            print('you choose an invaled day!')
            days = input('which day you want to see its data: Sunday, Monday, Tuesday, Tednesday, Thursday, Friday, Saturday? ').title()
    else:
        day = 'all'
    print('-'*40)
    return city, month, day


def load_data(city, month, day):
    """
    Loads data for the specified city and filters by month and day if applicable.
    Args:
        (str) city - name of the city to analyze
        (str) month - name of the month to filter by, or "all" to apply no month filter
        (str) day - name of the day of week to filter by, or "all" to apply no day filter
    Returns:
        df - Pandas DataFrame containing city data filtered by month and day
    """
    # Load data file into a dataframe
    df = pd.read_csv(CITY_DATA[city])
    # Convert Start Time column into datatime
    df['Start Time'] = pd.to_datetime(df['Start Time'])
    # Extracrt month and day of week from Start Time column to create a new column
    df['month'] = df['Start Time'].dt.month
    df['day_of_week'] = df['Start Time'].dt.day_name()
    # filter by month if applicable
    if month != 'all':
        # use the index of the months list to get the corresponding int
        months = ['january', 'february', 'march', 'april', 'may', 'june']
        month = months.index(month) + 1
        # filter by month to creat the new dataframe
        df = df[df['month'] == month]
    # filter by day if applicable
    if day != 'all':
        # filter by day of week to creat the new dataframe
        df = df[df['day_of_week'] == day.title()]
    return df


def time_stats(df):
    """Displays statistics on the most frequent times of travel."""

    print('\nCalculating The Most Frequent Times of Travel...\n')
    start_time = time.time()

    # TO DO: display the most common month
    months = ['january','february','march','april','may','june']
    month = df['month'].mode()[0]
    print(f'the most common month is: {months[month -1]}')
    # TO DO: display the most common day of week
    day = df['day_of_week'].mode()[0]
    print(f'the most common day is: {day}')
    # TO DO: display the most common start hour
    df['hour'] = df['Start Time'].dt.hour
    popular_hour = df['hour'].mode()[0]
    print(f'the most common hour is: {popular_hour}')
    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)


def station_stats(df):
    """Displays statistics on the most popular stations and trip."""

    print('\nCalculating The Most Popular Stations and Trip...\n')
    start_time = time.time()

    # TO DO: display most commonly used start station
    popular_start_station  = df['Start Station'].mode()[0]
    print(f'the most commonly used start station is: {popular_start_station}')
    # TO DO: display most commonly used end station
    popular_end_station = df['End Station'].mode()[0]
    print(f'the most commonly used end station is: {popular_end_station}')
    # TO DO: display most frequent combination of start station and end station trip
    popular_trip = 'from ' + df['Start Station'] + ' to ' +df['End Station']
    print(f'the most popular trip is: {popular_trip.mode()[0]}')
    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)


def trip_duration_stats(df):
    """Displays statistics on the total and average trip duration."""
    from datetime import timedelta as td
    
    print('\nCalculating Trip Duration...\n')
    start_time = time.time()
    # TO DO: display total travel time
    total_travel_time = (pd.to_datetime(df['End Time']) - pd.to_datetime(df['Start Time'])).sum()
    days = total_travel_time.days
    hours = total_travel_time.seconds // (60 * 60) 
    minutes = total_travel_time.seconds % (60 * 60) // 60
    seconds = total_travel_time.seconds % (60 * 60) % 60
    print(f'total travel time is: {days} days, {hours} hours, {minutes} minutes, {seconds} seconds')
    # TO DO: display mean travel time
    average_total_travel = (pd.to_datetime(df['End Time']) - pd.to_datetime(df['Start Time'])).mean()
    days = total_travel_time.days
    hours = total_travel_time.seconds // (60 * 60) 
    minutes = total_travel_time.seconds % (60 * 60) // 60
    seconds = total_travel_time.seconds % (60 * 60) % 60
    print(f'average total travel is: {days} days, {hours} hours, {minutes} minutes, {seconds} seconds')
    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)

def user_stats(df):
    """Displays statistics on bikeshare users."""

    print('\nCalculating User Stats...\n')
    start_time = time.time()

    # TO DO: Display counts of user types
    print(df['User Type'].value_counts())
    # TO DO: Display counts of gender
    if 'Gender' in df:
        gender = df['Gender'].value_counts()
        print(gender)
    else:
        print('There is no gender information in this city.')

    # TO DO: Display earliest, most recent, and most common year of birth
    if 'Birth Year' in df:
        earliest_birth = df['Birth Year'].min()
        print(earliest_birth)
        most_recent_birth = df['Birth Year'].max()
        print(most_recent_birth)
        most_common_birth = df['Birth Year'].mode()[0]
        print(most_common_birth)
    else:
        print('There is no gender information in this city.')
    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)
    #To aske user: if want to display data and print five row each time
def row_data(df):
    row_data =  0
    while True:
        answer = input('Do you want to dispaly the raw data? Yes or No').lower()
        if answer not in ['yes', 'no']:
            answer = input('you have type a wrong answer! select form : Yes or No').lower()
        elif answer == 'yes':
            row_data += 5
            print(df.iloc[row_data: row_data +5])
            answer_more = ('Do you want to see more row data? Yes or No').lower()
            if answer_more == 'no':
                break
        elif answer == 'no':
            return
def main():
    while True:
        city, month, day = get_filters()
        df = load_data(city, month, day)

        time_stats(df)
        station_stats(df)
        trip_duration_stats(df)
        user_stats(df)
        row_data(df)

        restart = input('\nWould you like to restart? Enter yes or no.\n')
        if restart.lower() != 'yes':
            break


if __name__ == "__main__":
	main()
