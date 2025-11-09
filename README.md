Report bugs you notice on https://www.apkmirror.com using this bug tracker.

Go to [the list of issues](../../issues) to see the current list or [file a new bug](../../issues/new).
// Source - https://stackoverflow.com/q/11953359
// Posted by Android Novice, modified by community. See post 'Timeline' for change history
// Retrieved 2025-11-09, License - CC BY-SA 3.0

    import android.graphics.Bitmap;
    import android.os.Bundle;
    import android.os.Parcelable;
    import android.support.v4.view.PagerAdapter;
    import android.support.v4.view.ViewPager;
    import android.view.LayoutInflater;
    import android.view.Menu;
    import android.view.MenuItem;
    import android.view.View;
    import android.view.animation.Animation;
    import android.view.animation.AnimationUtils;
    import android.widget.ImageView;
    import android.widget.ProgressBar;
    import android.widget.Toast;

    import com.nostra13.universalimageloader.core.DisplayImageOptions;
    import com.nostra13.universalimageloader.core.assist.FailReason;
    import com.nostra13.universalimageloader.core.assist.ImageLoadingListener;
    import com.nostra13.universalimageloader.core.assist.ImageScaleType;


    public class ImageActivity extends BaseActivity {

        private DisplayImageOptions imageoptions;

        private ViewPager imagepager;

        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);

            setContentView(R.layout.imagepager);


            Bundle bundle = getIntent().getExtras();
            String[] imageUrls = bundle.getStringArray(Extra.IMAGES);
            int pagerPosition = bundle.getInt(Extra.IMAGE_POSITION, 0);
            imageoptions = new DisplayImageOptions.Builder()
                .showImageForEmptyUri(R.drawable.noimage)
                .cacheOnDisc()
                .imageScaleType(ImageScaleType.EXACT)
                .build();

            imagepager= (ViewPager) findViewById(R.id.imagepager);
            imagepager.setAdapter(new ImagePagerAdapter(imageUrls));
            imagepager.setCurrentItem(pagerPosition);
        }


 public void setWall() {

    WallpaperManager myWallpaperManager
     = WallpaperManager.getInstance(getApplicationContext());
    try {
     myWallpaperManager.setResource(R.drawable.app_icon); //<--My app just set my app icon image as wallpaper, this is not I wanted. I wanted to set my selected image as wallpaper 
    } catch (IOException e) {
     // TODO Auto-generated catch block
     e.printStackTrace();
    }

}

@Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.main_menu, menu);
        return true;
    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
            case R.id.setWall:
                setWall();
                return true;
            default:
                return false;
        }
    }

@Override
    protected void onStop() {
        imageLoader.stop();
        super.onStop();
    }

    private class ImagePagerAdapter extends PagerAdapter {

        private String[] images;
        private LayoutInflater inflater;

        ImagePagerAdapter(String[] images) {
            this.images = images;
            inflater = getLayoutInflater();
        }

        @Override
        public void destroyItem(View container, int position, Object object) {
            ((ViewPager) container).removeView((View) object);
        }

        @Override
        public void finishUpdate(View container) {
        }

        @Override
        public int getCount() {
            return images.length;
        }

        @Override
        public Object instantiateItem(View view, int position) {
            final View imageLayout = inflater.inflate(R.layout.item_pager_image, null);
            final ImageView imageView = (ImageView) imageLayout.findViewById(R.id.image);
            final ProgressBar spinner = (ProgressBar) imageLayout.findViewById(R.id.loading);

            imageLoader.displayImage(images[position], imageView, imageoptions, new ImageLoadingListener() {
                public void onLoadingStarted() {
                    spinner.setVisibility(View.VISIBLE);
                }

                public void onLoadingFailed(FailReason failReason) {
                    String message = null;
                    switch (failReason) {
                        case IO_ERROR:
                            message = "Input/Output error";
                            break;
                        case OUT_OF_MEMORY:
                            message = "Out Of Memory error";
                            break;
                        case UNKNOWN:
                            message = "Unknown error";
                            break;
                    }
                    Toast.makeText(ImagePagerActivity.this, message, Toast.LENGTH_SHORT).show();

                    spinner.setVisibility(View.GONE);
                    imageView.setImageResource(android.R.drawable.ic_delete);
                }

                public void onLoadingComplete(Bitmap loadedImage) {
                    spinner.setVisibility(View.GONE);
                    Animation anim = AnimationUtils.loadAnimation(ImagePagerActivity.this, R.anim.fade_in);
                    imageView.setAnimation(anim);
                    anim.start();
                }

                public void onLoadingCancelled() {
                    // Do nothing
                }
            });

            ((ViewPager) view).addView(imageLayout, 0);
            return imageLayout;
        }

        @Override
        public boolean isViewFromObject(View view, Object object) {
            return view.equals(object);
        }

        @Override
        public void restoreState(Parcelable state, ClassLoader loader) {
        }

        @Override
        public Parcelable saveState() {
            return null;
        }

        @Override
        public void startUpdate(View container) {
        }
    }





}
